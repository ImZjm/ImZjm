---
title: Qexo部署到服务器
date: 2024-04-22 13:03:15
tags: ['Qexo', 'Hexo']
cover: https://cdn.jsdelivr.net/gh/ImZjm/ImZjm@Data/images%2F24%2F04%2F22%2Fqexo_1713763222.png
---

<img src="https://avatars.githubusercontent.com/u/103436983?s=200&v=4" alt="Hexo" width="100px"/>

# Qexo

> 一个美观、强大的在线 静态博客 管理器

- 简单：支持使用 Vercel 零成本一键部署, 您只需要配置一个免费数据库
- 强大：无限的拓展性, 应有尽有的配套功能尽在掌握
- 美观：基于 Bootstrap & Argon 的现代化响应式风格
- 全面：Django 后端, 支持多种平台和数据库

Qexo [官方文档](https://www.oplog.cn/qexo/)

# 服务器准备

- 服务商：亚马逊云
- 产品：EC2
- 系统：Ubuntu Server 22.04 LTS - 其他系统可能有所差异

# 安装 Python3

执行以下命令一键安装 python3 和 pip3
```bash
sudo apt update && sudo apt install -y python3 python3-pip
```

分别通过以下命令查看是否安装成功

- `python3 --version`
- `pip3 --version`

# 安装 MySQL 数据库

## 安装 MySQL

在 1Panel 应用商店中找到 MySQL，点击安装
版本选择 `5.7`
**端口外部访问**可以不用开(开了也用不到它)
![MySQL 安装](https://cdn.jsdelivr.net/gh/ImZjm/ImZjm@Data/images%2F24%2F04%2F22%2Fimage-20240422150303440_1713769383.png)

## 添加数据库

数据库安装成功后，点击菜单栏的`数据库`来到`MySQL`的管理页面
点击创建数据库
![创建数据库](https://cdn.jsdelivr.net/gh/ImZjm/ImZjm@Data/images%2F24%2F04%2F22%2Fimage-20240422150802315_1713769682.png)
**名称**和**用户名**随意填，比如都填 **hexo**，然后确认创建

# 安装 Qexo

## 下载 Qexo

{% tabs 下载 Qexo %}
<!-- tab 通过 git 下载 -->
**执行以下命令，自动从 github 将仓库克隆到本地**

```bash
git clone https://github.com/Qexo/Qexo.git ~/qexo
```
这将从 [github](https://github.com/Qexo/Qexo) 克隆最新的源码到 /home/ubuntu/qexo 文件夹中
<!-- endtab -->

<!-- tab 手动下载并解压 -->
**从 github 下载最新发行版并解压**

1. 在 [Release](https://github.com/am-abudu/Qexo/releases) 下载最新的版本 *Source code (zip)* 到电脑上

2. 通过 1Panel 上传并解压
   ![上传压缩包](https://cdn.jsdelivr.net/gh/ImZjm/ImZjm@Data/images%2F24%2F04%2F22%2Fimage-20240422145138846_1713768698.png)

   刷新页面后可以看到文件，点击解压
   ![上传的文件](https://cdn.jsdelivr.net/gh/ImZjm/ImZjm@Data/images%2F24%2F04%2F22%2Fimage-20240422145451879_1713768891.png)

<!-- endtab -->
{% endtabs %}

## 配置 Qexo

在 1Panel 中找到 qexo 的下载目录

1. 创建名为`configs.py`的文件
   <img src="https://cdn.jsdelivr.net/gh/ImZjm/ImZjm@Data/images%2F24%2F04%2F22%2Fimage-20240422151625465_1713770185.png" alt="创建文件"/>
   
2. 编辑`configs.py`，添加以下内容
   
   ```python
   import pymysql
   pymysql.install_as_MySQLdb()
   #域名
   DOMAINS = ["127.0.0.1", "localhost"]
   DATABASES = {
       'default': {
               'ENGINE': 'django.db.backends.mysql',
               #数据库名
               'NAME': 'qexo',
               #用户名
               'USER': 'qexo',
               #密码
               'PASSWORD': 'KFCvivo50',
               #数据库所在主机地址
               'HOST': '127.0.0.1',
               #端口
               'PORT': '3306',
               'OPTIONS': {
                   "init_command": "SET sql_mode='STRICT_TRANS_TABLES'"
               }
       }
   }
   ```
   
   - **DOMAINS** 为可以访问到 Qexo 域名或 ip，如果访问的域名或 ip 没有在这里定义，访问会被拦截
     这里建议不做修改，就填 `127.0.0.1` 或 `localhost`
   
   - 将 **数据库名** ，**用户名** 和 **密码** 修改为 [#添加数据库](#添加数据库) 时填入的内容
   
   点击确认保存
   
2. 返回上一级目录(qexo的安装目录)
   修改 qexo 文件夹及其子文件夹的归属为 **ubuntu**
   ![修改 qexo 文件夹权限](https://cdn.jsdelivr.net/gh/ImZjm/ImZjm@Data/images%2F24%2F04%2F22%2Fimage-20240422155449965_1713772490.png)
   
4. 执行 `cd ~/qexo` 来到 qexo 的安装目录，再执行以下命令进行数据初始化
   ```bash
   bash migrate.sh
   ```

# 启动 Qexo

   ## 安装 Supervisor

[Supervisor](https://github.com/Supervisor/supervisor) 是一款开源的进程管理工具，它可以实现进程的自动启动、重启、监控等功能，并可以方便地实现进程的异步启动和自动化管理。
这里需要它来帮助我们管理 qexo 的进程

```bash
sudo apt update && sudo apt install supervisor
```

执行命令一键安装 supervisor
在 1Panel 面板中依次点击 `工具箱` -> `进程守护`{% hideInline 如果显示 <b>未检测到 Supervisor 服务</b>，说明Supervisor未安装或安装失败，请重试上面的安装命令, 鸡你太美,gray,transparent %}

点击 **初始化**，使用默认配置，点击确认保存
![进程守护初始化](https://cdn.jsdelivr.net/gh/ImZjm/ImZjm@Data/images%2F24%2F04%2F22%2Fimage-20240422162607300_1713774367.png)
再次确认初始化

## 创建 Qexo 进程守护

点击 **`创建守护进程`**
![qexo 进程配置](https://cdn.jsdelivr.net/gh/ImZjm/ImZjm@Data/images%2F24%2F04%2F22%2Fimage-20240422163113837_1713774673.png)

- 名称随意，比如填 `114514`
- 启动用户填 `ubuntu`
- 如上图选择运行目录
- 启动命令填 `python3 manage.py runserver 0.0.0.0:8000 --noreload`
  这将在 **8000** 端口上启动 Qexo 服务
- 进程数填 `1`

最后确认保存
这时可以通过 [#配置 Qexo](#配置-Qexo) 时第  **`2`**  步配置的域名(或 公有ip) 加端口打开 Qexo面板
![Qexo 初始化页面](https://cdn.jsdelivr.net/gh/ImZjm/ImZjm@Data/images%2F24%2F04%2F22%2Fimage-20240422171559783_1713777359.png)

# (可选)反向代理 Qexo

{% hideToggle 反向代理 Qexo,bg,color %}

<font color="red" size="4em">如果没有域名，建议直接跳过这一步</font>

为了方便对 Qexo 进行管理，可以在 1Panel 面板新建一个网站来反向代理 Qexo
在 1Panel 的网站页面选择 **`创建网站`** -> **`反向代理`** 

- 主域名填自己的域名(不能与博客相同，比如解析一个qexo-admin.example.com)

- 代号随意，比如 `qexo`

- 代理地址填 [#配置 Qexo](#配置-Qexo) 时第  **`2`**  步配置的 ip + [创建 Qexo 进程守护](#创建-Qexo-进程守护) 时，命令中使用的端口
  例: `127.0.0.1:8000`

<img src="https://cdn.jsdelivr.net/gh/ImZjm/ImZjm@Data/images%2F24%2F04%2F22%2Fimage-20240422174126164_1713778886.png" width="50%" alt="qexo 反向代理创建"/>

再点击 `配置` 进入网站的配置界面
![配置反向代理](https://cdn.jsdelivr.net/gh/ImZjm/ImZjm@Data/images%2F24%2F04%2F22%2Fimage-20240422174619324_1713779179.png)

将后端域名改为 `127.0.0.1` 或 `localhost` - 因为 [#配置 Qexo](#配置-Qexo) 时，我的 `DOMAINS` 填的 `127.0.0.1`
![修改后端域名](https://cdn.jsdelivr.net/gh/ImZjm/ImZjm@Data/images%2F24%2F04%2F22%2Fimage-20240422174917423_1713779357.png)

<font color="#00ff00">这时，可以直接使用 域名 来访问 Qexo 面板</font>

{% endhideToggle %}

# Qexo 面板初始化

通过**域名**或 **ip** 打开 Qexo 面板，首次打开会跳转到初始化页面，点击开始初始化
![Qexo 博客配置](https://cdn.jsdelivr.net/gh/ImZjm/ImZjm@Data/images%2F24%2F04%2F22%2Fimage-20240422181210729_1713780730.png)

- 服务商选 `本地`，表示 hexo 与 qexo 都部署在同一台服务器上
  其他服务商请按要求填写
- 使用配置选择 `Hexo`
- 博客路径填 `Hexo` 的源代码的绝对路径，例如`/home/ubuntu/hexoblog`
  注意: 不是静态资源的路径。如果博客是使用一键部署插件部署到服务器的，请按照 [#Hexo](http://localhost:4000/blog/tags/Hexo/) 中的部署方式部署 Hexo 博客到服务器
  因为一键部署只会推送静态资源到服务器，Qexo 需要 Hexo 源代码才能修改博客内容
- 自动部署填 `hexo clean && hexo g`
  这会自动生成静态资源，并将静态资源放在 hexo 根配置文件中指定的目录(网站目录)中

点击下一步，即可通过方才设置的账号和密码登录面板
![Qexo 面板](https://cdn.jsdelivr.net/gh/ImZjm/ImZjm@Data/images%2F24%2F04%2F22%2Fimage-20240422182943222_1713781783.png)

<div style="text-align: center"><font size="10em" color="#00ff00">恭喜，你的 Qexo 部署成功了!</font></div>
