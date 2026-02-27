---
title: "PVE系列-LXC容器磁盘扩容"
subtitle: ""
date: 2024-03-02T17:38:35+08:00
lastmod: 2024-03-02T17:38:35+08:00
slug: pve-lxc-container-disk-grow
description: "以下内容是记录LXC容器磁盘如何进行扩容操作"
keywords:
  - "LXC磁盘扩容"
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

**PVE系列-LXC容器磁盘扩容**

以下内容是记录LXC容器磁盘如何进行扩容操作

 首先，选中要进行磁盘扩容的容器，停止它

![image-20240229094323500](./assets/image-20240229094323500-1741313500422-1-1741333887025-1.png)

 打开`shell`终端，检查要扩容逻辑卷是否有问题，笔者这里是vm-100-disk-1

![image-20240229094612636](./assets/image-20240229094612636-1741313500422-2-1741333887025-2.png)

```sh
# 查看内部逻辑卷的输出信息 
lvs 
#e2fsck检查该逻辑卷有无问题 
e2fsck -f /dev/pve/vm-100-disk-1
```

 没有问题的话，执行逻辑卷扩容操作

```sh
# 为逻辑卷增加100G容量 
lvextend -L +100G /dev/pve/vm-100-disk-0
```

 逻辑卷扩容完毕后，我们需要修改容器配置信息。若不修改，页面上显示的还是100G

![image-20240229095127925](./assets/image-20240229095127925-1741313500422-5-1741333887025-3.png)

```sh
vim /etc/pve/local/lxc/100.conf
```

 修改完毕后，并没有完成扩容操作，我们还需要进行实际扩容工作。

```sh
#使用resize2fs进行实际容量扩容 resize2fs /dev/pve/vm-100-disk-1 200G
```

 注意这里200G是增加后的总容量。

 完成后，进入PVE启动容器，查看是否扩容完成。

![image-20240229095202747](./assets/image-20240229095202747-1741313500422-3-1741333887025-5.png)

![image-20240229095221190](./assets/image-20240229095221190-1741313500422-4-1741333887025-4.png)

Done~~完成收工~
