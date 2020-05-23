---
description: GCP Centos 8 安装 v2ray，实验成功记录
---

# WebSocket + TLS + Web

**强烈建议：SSH登录VPS后，使用打开端口命令，先把22端口永久打开，否则在安装BBR后重启VM，将有可能无法登录SSH了，VPS就变砖就没法玩了。**

1，切换root，后续重启后也需要执行

```text
sudo -i
```

2，开启22端口（重要）

```text
systemctl start firewalld && firewall-cmd --zone=public --add-port=22/tcp --permanent && firewall-cmd --reload
```

```text
systemctl start firewalld && firewall-cmd --zone=public --add-port=443/tcp --permanent && firewall-cmd --reload
```

以下均以10000端口号为例子，需要将所有10000号改为自己的

```text
systemctl start firewalld && firewall-cmd --zone=public --add-port=10000/tcp --permanent && firewall-cmd --reload
```

```text
setsebool -P httpd_can_network_connect 1
```

3，安装常用命令

```text
yum install wget -y && yum install tar && yum install -y zip unzip
```

4，GCP默认已安装BBR加速，用以下命令执行查看

```text
uname -r
```

5，使用LNMP一键安装，仅安装 nginx

```text
wget http://soft.vpser.net/lnmp/lnmp1.6-full.tar.gz -cO lnmp1.6-full.tar.gz && tar zxf lnmp1.6-full.tar.gz && cd lnmp1.6-full && ./install.sh nginx
```

6，添加虚拟主机

```text
lnmp vhost add
```

安装SSL证书时，输入 2 选择 let's encrypt。

7，编辑网站的 conf 文件

```text
vi /usr/local/nginx/conf/vhost/vpn.mydomain.com.conf
cat /usr/local/nginx/conf/vhost/vpn.mydomain.com.conf
/etc/init.d/nginx restart
```

使用VI命名后，把原有代码注释掉，修改下方的相关参数（4个地方），再COPY进去。

```text
server {
  listen 443 ssl;
  listen [::]:443 ssl;
  
  ssl_certificate       /etc/v2ray/v2ray.crt;
  ssl_certificate_key   /etc/v2ray/v2ray.key;
  # 1. 这里需要修改。
  ssl_session_timeout 1d;
  ssl_session_cache shared:MozSSL:10m;
  ssl_session_tickets off;
  
  ssl_protocols         TLSv1.2 TLSv1.3;
  ssl_ciphers           ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES128-GCM-SHA256:ECDHE-ECDSA-AES256-GCM-SHA384:ECDHE-RSA-AES256-GCM-SHA384:ECDHE-ECDSA-CHACHA20-POLY1305:ECDHE-RSA-CHACHA20-POLY1305:DHE-RSA-AES128-GCM-SHA256:DHE-RSA-AES256-GCM-SHA384;
  ssl_prefer_server_ciphers off;
  
  server_name           mydomain.me; # 2. 这里需要修改。
    location /ray { # 3. 与 V2Ray 配置中的 path 保持一致
      if ($http_upgrade != "websocket") { 
          return 404;
      }
      proxy_redirect off;
      proxy_pass http://127.0.0.1:10000; # 4. 假设WebSocket监听在环回地址的10000端口上
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

8，安装 v2ray 

```text
wget https://install.direct/go.sh && bash go.sh && systemctl start v2ray && systemctl enable v2ray
```

9，编辑 v2ray 的设置

```text
cat /etc/v2ray/config.json
vi /etc/v2ray/config.json
```

修改以下 3 处，并替换

```text
{
  "inbounds": [
    {
      "port": 10000, //这个端口是假设的，自己设置，但要统一。
      "listen":"127.0.0.1",
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

以上应该要修改证书路径，网站地址，和**10000**端口。

10，客户端配置

使用 v2rayN 添加即可，注意网址是网址，端口是443，network，路径，tls要启用。

11，注意事项：

A，然后在GCP的防火墙里，把这些端口也打开。

B，Nginx修改后，需要重启 Nginx

```text
/etc/init.d/nginx restart
```

C，不重启VM的情况，需要执行以下命令重启 V2RAY

```text
systemctl restart v2ray
```

D，再次重复特别注意的内容。

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

