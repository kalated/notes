---
description: 零基础起步，高手请离开。
---

# 使用gitbook导出HTML网站

### 需求解析

纯静态网站（导出后放在自己的服务器上），笔记，电子书。

以下根据一步步的操作来执行，一定可以成功。

### 第一步：github上的操作

创建github账户，并新建一个仓库，譬如命名 book001（不需要放任何内容）

### 第二步：gitbook上操作

创建gitbook账户（使用github账号关联），接着创建一个space，可以尝试添加一些内容（至少上传一张图片）。这样在线的book就完成了。

点击 Integrations ，打开 Github 开关，将电子书同步导入到 github 的新建仓库book001中。

> 注意：gitbook和github不是一个网站，它们是相对独立的。

### 第三步：在自己电脑上的操作

### 1,安装 node.js

 [在 Windows、Mac OS X 與 Linux 中安裝 Node.js 網頁應用程式開發環境](http://www.gtwang.org/2013/12/install-node-js-in-windows-mac-os-x-linux.html)

### 2, 安装 gitbook

打开 cmd，执行以下命令。

```text
npm install gitbook-cli -g
```

等待足够的时间，再执行

```text
gitbook -V
```

### 第四步：从 Github 下载zip

使用 clone or download 按钮，下载ZIP文件到电脑上，并将其解压到 F 盘的 www 文件夹内。同时在 www 里建立一个名为 newbook 文件夹。

在 cmd 里执行以下命令：

```text
gitbook build www www/newbook
```

这样纯 html 的电子书就生成在 newbook 文件夹内了，但是页面内的图片无法显示。

### 第五步：下载和使用 grepWin

Gitbook 自动生成了 .gitbook/assets 来存放图片，而以 . 开头的文件夹在一般 web 环境里是不显示的，所以图片会有 403 错误。譬如把 .gitbook 改为 **source**。

 使用 grepWin 可以批量把 HTML 文件内的 .gitbook/assets 替换成 **source**/assets。

### 第六步：上传

将 html 文件上传到网站目录下，就可以访问了。  🙉 

