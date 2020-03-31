---
description: 适用CENTOS系统
---

# V2ray常规安装

操作几遍后，发现真的不难弄，前后只要10分钟就可以了。目前来看香港节点的访问速度很好，YOUTUBE无压力。

```text
##安装WGET
yum install wget -y

## 下载和安装BBR加速
wget --no-check-certificate -O tcp.sh https://github.com/cx9208/Linux-NetSpeed/raw/master/tcp.sh && chmod +x tcp.sh && ./tcp.sh

## 选择 2 进行安装，等待重启，然后执行以下继续安装
./tcp.sh

## 选择 7 进行安装，完成BBR加速安装。

## 安装v2ray
bash <(curl -L -s https://install.direct/go.sh)

## 启动 V2Ray 进程；
service v2ray start

## 之后可以使用以下控制 V2Ray 的运行
service v2ray start|stop|status|reload|restart|force-reload

## 设备开机自动启动V2RAY
systemctl enable v2ray

## 查看端口和ID并记下
cat /etc/v2ray/config.json

## 开启端口(26947为演示，请改为自己的)
systemctl start firewalld && firewall-cmd --zone=public --add-port=26947/tcp --permanent && firewall-cmd --reload

## 电脑上下载V2RAYN应用程序123根据实际情况填写，其它的和图片一致即可。
```

![](../.gitbook/assets/image%20%284%29.png)

