# 2020最新版V2fly ws+tls+官方搭建教程

1: 时间同步： Centos系统

```text
date -R
timedatectl set-local-rtc 1
timedatectl set-timezone Asia/Shanghai
```

2：[V2fly新版项目地址](https://github.com/v2fly/fhs-install-v2ray) 安装依赖：CentOS

```text
yum makecache
yum install curl
```

3:安装源码：

```text
curl -O https://raw.githubusercontent.com/v2fly/fhs-install-v2ray/master/install-release.sh
curl -O https://raw.githubusercontent.com/v2fly/fhs-install-v2ray/master/install-dat-release.sh
```

4:安裝和更新 V2Ray

```text
bash install-release.sh
```

5:安裝最新發行的 geoip.dat 和 geosite.dat

```text
bash install-dat-release.sh
```

6：移除 V2Ray

```text
bash install-release.sh --remove
```

7：创建配置文件

```text
vi /usr/local/etc/v2ray/config.json
```

配置文件内容：

```text
{
  "inbounds": [
    {
      "port": 443, // 服务器端口
      "protocol": "vmess",    
      "settings": {
        "clients": [
          {
            "id": "UUID", // V2ray客户端UUID 
            "alterId": 64
          }
        ]
      },
      "streamSettings": {
        "network": "ws", // TCP或WS
        "security": "tls", // security 要设置为 tls 才会启用 TLS
        "tlsSettings": {
          "certificates": [
            {
              "certificateFile": "/etc/v2ray/v2ray.crt", // 证书文件路径
              "keyFile": "/etc/v2ray/v2ray.key" // 密钥文件路径
            }
          ]
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

8:安装软件依赖 CentOS系统:

```text
yum update && yum install curl -y && yum install cron -y && yum install socat -y
```

9:安装 acme.sh:

```text
curl https://get.acme.sh | sh
source ~/.bashrc
```

10:申请证书：

```text
bash ~/.acme.sh/acme.sh --issue -d 域名 --alpn -k ec-256
```

11:安装证书：

```text
mkdir /etc/v2ray && sudo ~/.acme.sh/acme.sh --installcert -d 域名 --fullchainpath /etc/v2ray/v2ray.crt --keypath /etc/v2ray/v2ray.key --ecc
```

12:证书权限设置：

```text
chmod 644 /etc/v2ray/v2ray.key
```

13:设置重启\|状态检查\|开机自启

```text
systemctl restart v2ray
systemctl status v2ray
systemctl enable v2ray
```

