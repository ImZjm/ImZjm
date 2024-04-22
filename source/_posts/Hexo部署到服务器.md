---
title: Hexo部署到服务器
date: 2024-04-21 21:42:13
tags: ['Hexo']
categories: ['Hexo 博客']
---

<img src="https://raw.githubusercontent.com/hexojs/logo/master/hexo-logo-avatar.png" alt="Hexo" width="100px"/>

# 服务器准备

- 服务商：亚马逊云
- 产品：EC2
- 系统：Ubuntu Server 22.04 LTS - 其他系统可能有所差异

# 安装 Hexo

## 安装 1Panel

- 1Panel 是新一代的 Linux 服务器运维管理面板，可以简化大量操作

- 安装命令：

```bash
curl -sSL https://resource.fit2cloud.com/1panel/package/quick_start.sh -o quick_start.sh && sudo bash quick_start.sh
```

根据提示完成自动安装

<img src="https://cdn.jsdelivr.net/gh/ImZjm/ImZjm@Data/images/24/04/22/image-20240422001512403_1713716112.png" alt="1Panel 安装成功" width="100%"/>

通过外网地址即可打开面板

![1Panel 登录界面](https://cdn.jsdelivr.net/gh/ImZjm/ImZjm@Data/images%2F24%2F04%2F22%2Fimage-20240422003314078_1713717194.png)

## (可选)安装 git

git 是一个的分布式版本控制系统
在ubuntu下，只需要简单的一行命令即可安装成功

```bash
sudo apt update && sudo apt install -y git
```

安装完成后可以通过命令 `git --version` 查看是否安装成功

## 安装 Node.js

Node.js 是 Hexo 的运行环境
通过以下命令安装

```bash
sudo apt update && sudo apt install -y nodejs npm
```

其中 npm 是 Node.js 的包管理器，需要它来安装 Hexo 的依赖和插件
安装完成后可以通过

- `node -v`
- `npm -v`

分别查看是否安装成功

### (可选)安装 cnpm

- cnpm 是一个基于 npm 的定制化工具，专为中国开发者设计。
- 它是淘宝团队推出的一个 npm 镜像源和客户端工具，旨在解决在中国地区使用 npm 时可能遇到的网络延迟和下载速度慢的问题。
- 阿里<a href="https://npmmirror.com/">官方镜像站</a>

通过以下命令即可安装 cnpm
```bash
npm install -g cnpm --registry=https://registry.npmmirror.com
```

安装成功后即可使用定制的 [cnpm](https://npmmirror.com/package/cnpm) 命令行工具代替默认的 npm
例如：

- `sudo npm install -g hexo-cli`，可以使用`sudo cnpm install -g hexo`-cli代替
- `npm install hexo-abbrlink2 --save`，可以使用`cnpm install hexo-abbrlink2 --save`代替

## 创建 Hexo 博客

通过以下命令安装 Hexo
```bash
sudo npm install -g hexo-cli
```

通过命令`cd ~`来到用户目录下，再执行以下命令创建一个 hexo 博客

```bash
hexo init hexoblog
```

这行命令会创建一个名为 hexoblog 的文件夹，并下载 hexo 博客初始文件
通过 1Panel 面板可以更直观的查看文件内容
![Hexo 博客文件夹](https://cdn.jsdelivr.net/gh/ImZjm/ImZjm@Data/images%2F24%2F04%2F22%2Fimage-20240422015123755_1713721883.png)

通过命令`cd hexoblog`进入到文件夹中，再执行以下命令来安装 hexo 所需的依赖
```bash
npm install
```

这个命令会将 package.json 文件中提到的所有依赖全部下载下来，保存到 node_modules 中

此时博客已经创建完成

# 部署静态博客

## 安装 OpenResty

OpenResty 是一个基于 Nginx 的高性能 Web 应用服务器，它将 Nginx 与 Lua 编程语言集成在一起，提供了强大的功能和灵活性。

回到 1Panel 面板，在应用商店中找到 OpenResty，点击安装
再点击确认，所有配置默认即可

![OpenResty 默认安装配置](https://cdn.jsdelivr.net/gh/ImZjm/ImZjm@Data/images%2F24%2F04%2F22%2Fimage-20240422020622001_1713722782.png)

## 创建网站

等待 OpenResty 安装成功后，依次点击 `网站` -> `网站` -> `创建网站` -> `静态网站`

![创建网站](https://cdn.jsdelivr.net/gh/ImZjm/ImZjm@Data/images%2F24%2F04%2F22%2Fimage-20240422021542190_1713723342.png)
在 `主域名` 中填入已经解析到服务器的域名
- 如果没有域名，则直接填入 ip 地址
- 如有多个域名，可以填到 `其他域名` 中

只有在这里指定的域名或 ip 才能访问到网站。其他域名即使解析到这台服务器，也无法访问到网站。

代号是生成文件夹的名称，根据自己习惯填写

最后点击确认，网站创建成功。此时可以通过域名或 ip 访问到网站，初始状态下是一个默认的页面

## 将 Hexo 部署到静态网站

### 更改文件夹权限
如下图：![网站管理列表](https://cdn.jsdelivr.net/gh/ImZjm/ImZjm@Data/images%2F24%2F04%2F22%2Fimage-20240422022821217_1713724101.png)
点击跳转到网站所在的目录

找到上一级目录
如下图依次点击或修改，将文件夹所有权给`ubuntu`
![站点](https://cdn.jsdelivr.net/gh/ImZjm/ImZjm@Data/images%2F24%2F04%2F22%2Fimage-20240422031713649_1713727033.png)
![更改权限](https://cdn.jsdelivr.net/gh/ImZjm/ImZjm@Data/images%2F24%2F04%2F22%2Fimage-20240422031946380_1713727186.png)

点击确认保存

### 更改部署配置

继续点击进入到 index 文件夹 - 所有的静态资源都要放在这个文件夹中

点击地址栏的空白处，复制完整的文件夹路径
![网站根目录](https://cdn.jsdelivr.net/gh/ImZjm/ImZjm@Data/images%2F24%2F04%2F22%2Fimage-20240422023415841_1713724455.png)
再进入先前创建在`/home/ubuntu`目录下的博客文件夹`hexoblog`
点击编辑`_config.yml`文件

![编辑 _config.yml 文件](https://cdn.jsdelivr.net/gh/ImZjm/ImZjm@Data/images%2F24%2F04%2F22%2Fimage-20240422024342588_1713725022.png)

将`public_dir`的值改为刚才复制静态网站目录，并确认保存文件
注意：

- 冒号后面有一个空格
- 目录以斜杠开头

这样，hexo 生成的静态页面就会保存到`public_dir`指定的目录中，以便我们访问

### 通过命令部署

通过 `cd ~/hexoblog` 来到 `/home/ubuntu/hexoblog` 中，再依次执行以下命令生成静态资源

```bash
hexo clean
```

```bash
hexo g
```

## 预览

回到网站的 `index` 目录下，可以看到 Hexo 将生成的静态文件全部放在了这里。
当通过域名或 ip 访问网站时，就会打开默认页面 - `index.html`
![生成的静态文件](https://cdn.jsdelivr.net/gh/ImZjm/ImZjm@Data/images%2F24%2F04%2F22%2Fimage-20240422032616282_1713727576.png)

尝试通过域名访问网站
![访问网站](https://cdn.jsdelivr.net/gh/ImZjm/ImZjm@Data/images%2F24%2F04%2F22%2Fimage-20240422032715714_1713727635.png)

<div style="text-align: center"><font size="10em" color="#00ff00">恭喜，你的网站创建成功了!</font></div>
