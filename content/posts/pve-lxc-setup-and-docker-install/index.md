---
title: PVE系列-初始化LXC容器并安装Docker
subtitle:
date: 2024-02-29T16:51:19+08:00
lastmod: 2024-03-01T16:51:19+08:00
slug: pve-lxc-setup-and-docker-install
author:
  name: w2422
  link: 
  email: z242235718@163.com
  avatar: https://2.gravatar.com/userimage/277888267/a5dd7d088669f51623b8b85bd96a1552?size=64
description: 下载模板前请务必确保已经替换过LXC源，若还未替换可参考《PVE系列-更换软件&容器源》进行更换LXC源操作。
keywords: "PVE"
comment: false
weight: 0
tags:
  - PVE
  - Coding
categories:
  - PVE
  - Coding
hiddenFromHomePage: false
hiddenFromSearch: false
hiddenFromRelated: false
hiddenFromFeed: false
summary:
featuredImagePreview:
featuredImage:
password:
message:
repost:
  enable: false
  url:

# See details front matter: https://fixit.lruihao.cn/documentation/content-management/introduction/#front-matter
---

<!--more-->



### 模板下载

 下载模板前请务必确保已经替换过LXC源，若还未替换可参考[《PVE系列-更换软件&容器源》](https://www.gvnote.com/posts/replace-lxc-container-source)进行更换LXC源操作。

 在PVE后台页面选择 `local(pve)` -> `CT模板` -> `模板` 选择对应镜像下载。这里以debian12为例：

![image-20240228104504044](./assets/image-20240228104504044-1741313025781-1-1741333679444-1.png)

 点击下载并等待下载完成。

![image-20240228105502754](./assets/image-20240228105502754-1741313025781-2-1741333679444-4.png)

### 创建LXC容器

 点击右上方`创建CT` 来创建LXC容器

![image-20240228105640475](./assets/image-20240228105640475-1741313025781-3-1741333679444-7.png)

 勾选`无特权的容器` 选项并填写配置信息

![image-20240228110312699](./assets/image-20240228110312699-1741313025781-4-1741333679444-2.png)

 模板选择刚下载好的debian12

![image-20240228110454078](./assets/image-20240228110454078-1741313025781-5-1741333679444-9.png)

 根据实际需要划分磁盘大小，笔者这边有存储需要，因此新增了一块存储磁盘

![image-20240228112447781](./assets/image-20240228112447781-1741313025781-7-1741333679444-3.png) ![image-20240228112539337](./assets/image-20240228112539337-1741313025781-6-1741333679444-5.png)

 CPU和内存分配根据实际需求划分即可。这边以运行gitlab为例，2C8G200G。

 网络配置

![image-20240228112646165](./assets/image-20240228112646165-1741313025781-8-1741333679444-6.png)

 若ipv6获取不到地址，切换为SLAAC尝试下

 DNS保持默认即可

![image-20240228112729055](./assets/image-20240228112729055-1741313025781-9-1741333679444-8.png)

 等待创建完成

![image-20240228112805860](./assets/image-20240228112805860-1741313025781-10-1741333679444-10.png)

### 启动LXC容器

 创建完成后，来到选项`控制台模式` 调整为` shell` ，否则启动后是黑屏状态。![image-20240228145000182](./assets/image-20240228145000182-1741313025781-11-1741333679444-11.png)

```
调整为shell后再点击启动。
```

![image-20240228143836850](./assets/image-20240228143836850-1741313025781-12-1741333679444-12.png)

 点开控制台，开启远程链接。

![image-20240228145421247](./assets/image-20240228145421247-1741313025781-13-1741333679444-13.png)

```shell
#允许远程登录 
sed -i '/PermitRootLogin/ a PermitRootLogin yes' /etc/ssh/sshd_config 
#重启ssh服务 
systemctl restart sshd`
```

 ok，使用`ssh终端`测试能否链接上

![image-20240228145833603](./assets/image-20240228145833603-1741313025781-14-1741333679444-14.png)

#### 初始化配置

**时区**

```shell
dpkg-reconfigure tzdata
```

![image-20240228150243234](./assets/image-20240228150243234-1741313025781-15-1741333679444-15.png)

**语言**

```sh
dpkg-reconfigure locales
```

![image-20240228150329403](./assets/image-20240228150329403-1741313025781-16-1741333679444-16.png)

**软件源更换**

 由于模板并未预装 curl，所以我们需要手动安装下

```sh
apt update apt install curl
```

![image-20240228150717838](./assets/image-20240228150717838-1741313025781-17-1741333679444-17.png)

 完成之后再执行下属命令

```sh
bash <(curl -sSL https://linuxmirrors.cn/main.sh)
```

![image-20240228150829822](./assets/image-20240228150829822-1741313025781-18-1741333679445-19.png)

### 核显配置

 如果你创建的LXC容器不需要使用核显的话，这一部分可以跳过。

 【注意】下述操作需要在**宿主机**操作

 使用命令`ls -l /dev/dri` 查询硬件信息

![image-20240228153102606](./assets/image-20240228153102606-1741313025781-19-1741333679445-18.png)

 这里的226,0和226,128是主次设备号

 编辑配置文件`/etc/pve/lxc/容器id.conf`

 我这边是 /etc/pve/lxc/100.conf

```sh
#新增下列内容 
lxc.cgroup2.devices.allow: c 226:0 
rwm lxc.cgroup2.devices.allow: c 226:128 rwm 
lxc.cgroup2.devices.allow: c 29:0 rwm 
lxc.mount.entry: /dev/dri dev/dri none bind,optional,create=dir 
lxc.mount.entry: /dev/fb0 dev/fb0 none bind,optional,create=file 
lxc.apparmor.profile: unconfined
```

 参数说明：

 `lxc.apparmor.profile: unconfined`用于关闭 LXC 容器的apparmor保护，开启状态无法安装 Docker

> Tips
>
>  核显这部分内容还尚未测试

### 安装Docker

 设置docker的apt源

```sh
sudo apt-get update 
sudo apt-get install ca-certificates curl 
sudo install -m 0755 -d /etc/apt/keyrings 
sudo curl -fsSL https://download.docker.com/linux/debian/gpg -o /etc/apt/keyrings/docker.asc sudo chmod a+r /etc/apt/keyrings/docker.asc
```

```sh
echo \  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/debian \  $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null 
sudo apt-get update
```



![image-20240228152123636](./assets/image-20240228152123636-1741313025781-20-1741333679445-20.png)

 安装docker软件包

![image-20240228152148834](./assets/image-20240228152148834-1741313025781-21-1741333679445-22.png)

```sh
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```



 验证是否安装成功

![image-20240228152201205](./assets/image-20240228152201205-1741313025781-22-1741333679445-21.png)

```sh
sudo docker run hello-world
```

 开机启动

![image-20240228152213417](./assets/image-20240228152213417-1741313025781-23-1741333679445-23.png)

```sh
systemctrl enable docker
```



#### docker-compose安装

 下载compose

![image-20240228152430215](./assets/image-20240228152430215-1741313025781-24-1741333679445-24.png)

```sh
curl -SL https://github.com/docker/compose/releases/download/v2.24.6/docker-compose-linux-x86_64 -o /usr/local/bin/docker-compose
```



 赋予权限

```sh
chmod +x /usr/local/bin/docker-compose
```



 测试

![image-20240228152852593](./assets/image-20240228152852593-1741313025781-25-1741333679445-25.png)

```sh
docker-compose
```



#### 配置Gitlab

 使用docker-compose的方式部署，这里直接放上配置文件

#### 优化gitlab

修改gitlab的配置，找到/etc/gitlab/gitlab.rb文件，添加下属配置：

```sh
#puma相关 
puma['worker_timeout'] = 60 
puma['worker_processes'] = 2 
puma['per_worker_max_memory_mb'] = 768 
#postgresql相关 
postgresql['max_worker_processes'] = 4 
postgresql['shared_buffers'] = "128MB" 
#关闭prometheus 
prometheus_monitoring['enable'] = false 
#sidekiq相关 
sidekiq['max_concurrency'] = 10
```



相关参数设置参考官方给出的配置

https://docs.gitlab.com/omnibus/settings/memory_constrained_envs.html
