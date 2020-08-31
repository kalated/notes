# 2020最新版V2fly ws+tls+官方搭建教程

1: 时间同步： Centos系统

```text
date -R
timedatectl set-local-rtc 1
timedatectl set-timezone Asia/Shanghai
```

2：[V2fly新版项目地址](https://github.com/v2fly/fhs-install-v2ray) 安装依赖：CentOS

3:安装源码：

4:安裝和更新 V2Ray

5:安裝最新發行的 geoip.dat 和 geosite.dat

6：移除 V2Ray

7：创建配置文件

配置文件内容：

8:安装软件依赖 CentOS系统:

9:安装 acme.sh:

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

