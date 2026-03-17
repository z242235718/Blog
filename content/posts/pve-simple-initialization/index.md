---
title: "PVE系列-初始化设置"
subtitle: ""
date: 2024-03-01T17:32:05+08:00
lastmod: 2024-03-01T17:32:05+08:00
slug: pve-simple-initialization
keywords:
  - "PVE初始化"
tags:
  - PVE
  - Coding
categories:
  - PVE
  - Coding
author: 
  name: w2422
  link: 
  email: z242235718@163.com
  avatar: https://2.gravatar.com/userimage/277888267/a5dd7d088669f51623b8b85bd96a1552?size=64
description: "首先使用data 和timedatectl 指令查看当前系统时间是否与你所在时区匹配，若匹配则不用修改"
license: ""
comment: true
weight: 0
summary: ""
featuredImagePreview:
featuredImage:
#  image: "images/cover.jpg" # 默认封面图路径
# FixIt 主题特有配置
hiddenFromHomePage: false
hiddenFromSearch: false
hiddenFromRelated: false
hiddenFromFeed: false
password:
message:
repost:
  enable: false
  url:
---

<!--more-->

## PVE系列-初始化设置

### 更新时区

 首先使用`data` 和`timedatectl` 指令查看当前系统时间是否与你所在时区匹配，若匹配则不用修改。

![image-20240228102040610](./assets/image-20240228102040610-1741313708654-1-1741333934898-1.png)

 若不匹配则使用`dpkg-reconfigure` 指令修改时区。

```sh
dpkg-reconfigure tzdata
```

 选择Asia即可

![image-20240228102130588](./assets/image-20240228102130588-1741313708654-4-1741333934898-2.png)

### 语言设置

 修改系统默认语言为中文

```sh
dpkg-reconfigure locales
```

 选择`zh_CN.UTF-8 UTF-8` (确认选定按下空格)

![image-20240228102512482](./assets/image-20240228102512482-1741313708654-2-1741333934898-3.png)

 确认选择`zh_CN.UTF-8 UTF-8`

![image-20240228102743958](./assets/image-20240228102743958-1741313708654-3-1741333934898-4.png)

### 硬件信息展示

 使用下属脚本完成在PVE页码上展示cpu、[磁盘](https://www.smzdm.com/ju/s2441lm/)等温度、频率、功率等信息

```sh
wget -c https://raw.githubusercontent.com/a904055262/PVE-manager-status/main/showtempcpufreq.sh && chmod +x showtempcpufreq.sh && ./showtempcpufreq.sh
```



### 软件源更换

 参考[《PVE更换软件&LXC容器源》](https://www.gvnote.com/posts/replace-lxc-container-source)

### 开启远程登录

 输入以下内容，允许终端登录，若有防火墙需要配置下防火墙规则，开放22端口。

```sh
#开启root登录 
sed -i '/PermitRootLogin/ a PermitRootLogin yes' /etc/ssh/sshd_config 
#重启ssh服务,让配置生效 
systemctrl restart sshd
```
