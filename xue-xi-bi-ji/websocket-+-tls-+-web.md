---
description: GCP Centos 8 安装 v2ray，实验成功记录
---

# WebSocket + TLS + Web

**强烈建议：SSH登录VPS后，使用打开端口命令，先把22端口永久打开，否则在安装BBR后重启VM，将有可能无法登录SSH了，VPS就变砖就没法玩了。**

```text
sudo -i
```

```text
systemctl start firewalld && firewall-cmd --zone=public --add-port=22/tcp --permanent && firewall-cmd --reload
```

```text
setsebool -P httpd_can_network_connect 1
```

```text
yum install wget -y && yum install tar
```

BBR加速，参考网址 [https://ssr.tools/1217](https://ssr.tools/1217)

```text
wget --no-check-certificate -O tcp.sh https://github.com/cx9208/Linux-NetSpeed/raw/master/tcp.sh && chmod +x tcp.sh && ./tcp.sh
```

```text
选择 2 进行安装，等待重启，然后执行以下
```

```text
./tcp.sh
```

```text
选择 7 进行安装，完成BBR加速安装。
```

1，使用腾讯云解析绑定域名 vpn.mydoain.com 到IP地址：10.10.10.20

2，使用腾讯云申请免费SSL证书，申请后下载到电脑上。

3，将 .crt 和 .key 上传到 vps 上，copy 到指定位置，以后更新就可以直接更新这里的就好。

4，使用LNMP一键安装，仅安装 nginx

切换root

```text
sudo -i
```

```text
wget http://soft.vpser.net/lnmp/lnmp1.6-full.tar.gz -cO lnmp1.6-full.tar.gz && tar zxf lnmp1.6-full.tar.gz && cd lnmp1.6-full && ./install.sh nginx
```

5，lnmp vhost add 添加虚拟主机，安装SSL证书时，输入刚刚证书放置的位置。

6，安装 v2ray 

```text
wget https://install.direct/go.sh
yum install -y zip unzip && bash go.sh && systemctl start v2ray

## 开机自启
systemctl enable v2ray
```

7，编辑 v2ray 的设置

```text
cat /etc/v2ray/config.json
vi /etc/v2ray/config.json
```

```text
{
  "inbounds": [
    {
      "port": 10000, //这个端口是假设的，自己设置，但要统一。
      "listen":"127.0.0.1",//只监听 127.0.0.1，避免除本机外的机器探测到开放了 10000 端口
      "protocol": "vmess",
      "settings": {
        "clients": [
          {
            "id": "b831381d-6324-4d53-ad4f-8cda48b30811这个UUID自己生成，要统一",
            "alterId": 64
          }
        ]
      },
      "streamSettings": {
        "network": "ws",
        "wsSettings": {
        "path": "/ray" //这个路径一定要这样的，不知道为什么。要统一
        }
      }
    }
  ],
  "outbounds": [
    {
      "protocol": "freedom",
      "settings": {}
    }
  ]
}
```

8，编辑网站的 conf 文件

```text
server {
  listen 443 ssl;
  listen [::]:443 ssl;
  
  ssl_certificate       /etc/v2ray/v2ray.crt;
  ssl_certificate_key   /etc/v2ray/v2ray.key;
  # 这个路径在3里讲过，5里输入操作过。
  ssl_session_timeout 1d;
  ssl_session_cache shared:MozSSL:10m;
  ssl_session_tickets off;
  
  ssl_protocols         TLSv1.2 TLSv1.3;
  ssl_ciphers           ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES128-GCM-SHA256:ECDHE-ECDSA-AES256-GCM-SHA384:ECDHE-RSA-AES256-GCM-SHA384:ECDHE-ECDSA-CHACHA20-POLY1305:ECDHE-RSA-CHACHA20-POLY1305:DHE-RSA-AES128-GCM-SHA256:DHE-RSA-AES256-GCM-SHA384;
  ssl_prefer_server_ciphers off;
  
  server_name           mydomain.me;
    location /ray { # 与 V2Ray 配置中的 path 保持一致
      if ($http_upgrade != "websocket") { # WebSocket协商失败时返回404
          return 404;
      }
      proxy_redirect off;
      proxy_pass http://127.0.0.1:10000; # 假设WebSocket监听在环回地址的10000端口上
      proxy_http_version 1.1;
      proxy_set_header Upgrade $http_upgrade;
      proxy_set_header Connection "upgrade";
      proxy_set_header Host $host;
      # Show real IP in v2ray access.log
      proxy_set_header X-Real-IP $remote_addr;
      proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    }
}
```

```text
vi /usr/local/nginx/conf/vhost/vpn.mydomain.com.conf
cat /usr/local/nginx/conf/vhost/vpn.mydomain.com.conf
/etc/init.d/nginx restart
```

以上应该要修改证书路径，网站地址，和10000端口。

9，客户端配置

使用 v2rayN 添加即可，注意网址是网址，端口是443，network，路径，tls要启用。

10，注意事项：

A，如何开启15254端口 ，不清楚情况下，把443也操作一下。代码如下：

```text
systemctl start firewalld
firewall-cmd --zone=public --add-port=15254/tcp --permanent
firewall-cmd --reload
```

B，然后在GCP的防火墙里，把这些端口也打开。

C，Nginx修改后，需要重启 Nginx

```text
/etc/init.d/nginx restart
```

D，BBR安装

E，再次重复特别注意的内容。

{% hint style="warning" %}
端口 \#VPS和防火墙开启

path 路径 \# /ray 

重启 NGINX 和 V2RAY

证书是否过期
{% endhint %}

> 最后，客户端设置。
>
> [https://github.com/2dust/v2rayN/releases](https://github.com/2dust/v2rayN/releases) 下载v2rayN.zip
>
> 安卓手机 [https://apkpure.com/v2rayng/com.v2ray.ang/](https://apkpure.com/v2rayng/com.v2ray.ang/)
>
> IPHONE需要先登录国外账号才能搜索下载APP，91VPN亲测可用。
>
> 参考教程： [https://www.4spaces.org/digitalocean-build-v2ray-0-1/](https://www.4spaces.org/digitalocean-build-v2ray-0-1/) [https://www.v2ray.com/](https://www.v2ray.com/)
>
> 重要参考：[https://guide.v2fly.org/advanced/wss\_and\_web.html](https://guide.v2fly.org/advanced/wss_and_web.html)

