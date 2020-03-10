---
description: 'https://blog.csdn.net/datadev_sh/article/details/79593360'
---

# 用SSH工具XShell连接谷歌云 root用户或普通用户

目录 1.以root用户登入 2.普通用户，秘钥登入

1.用root用户登入 1.1.进入谷歌云实例面板

1.2.切换到root角色 sudo -i 1 1.3.修改SSH配置文件/etc/ssh/sshd\_config vi /etc/ssh/sshd\_config 1 修改PermitRootLogin和PasswordAuthentication为yes

## Authentication:

PermitRootLogin yes //默认为no，需要开启root用户访问改为yes

## Change to no to disable tunnelled clear text passwords

PasswordAuthentication yes //默认为no，改为yes开启密码登陆 1 2 3 4 5 1.4.给root用户设置密码 passwd root 1 1.5.重启SSH服务使修改生效 /etc/init.d/ssh restart 1 1.6.登录 在xshell中，直接使用root账号密码登录。

2.新建普通用户登入 2.1. 本地用xshell生成密秘钥

2.2. 将秘钥配置到谷歌云上 菜单 — 计算引擎 — 元数据 — SSH秘钥 — 修改 — 添加一项

粘贴刚才从xshell复制的秘钥。在末尾添加 \[空格\]\[用户名\] 这里就是“ google”，保存即可。

2.3. 用xshell连接

连上之后，输入命令 sudo -i切换到root用户. ———————————————— 版权声明：本文为CSDN博主「datadev\_sh」的原创文章，遵循 CC 4.0 BY-SA 版权协议，转载请附上原文出处链接及本声明。 原文链接：[https://blog.csdn.net/datadev\_sh/article/details/79593360](https://blog.csdn.net/datadev_sh/article/details/79593360)

