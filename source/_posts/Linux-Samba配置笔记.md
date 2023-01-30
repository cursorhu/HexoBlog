---
title: Linux Samba配置笔记
date: 2023-01-30 10:55:24
tags: samba
categories: linux

---

## Samba基本概念

Samba是SMB protocol的一种应用实现，分为服务端和客户端；

Samba通常的使用场景：在同一局域网内的的Linux主机安装Samba服务，windows主机可以访问Linux Samba服务指定的共享目录。

在嵌入式开发中通常在windows 上编辑Samba共享目录下的代码，通过 Linux环境编译代码，而无需在两个主机间拷贝代码文件。

## Linux安装Samba服务

Linux Ubuntu 22 LTS版本 安装Samba服务参考：

[www.how2shout.com/linux/how-to-install-samba-on-ubuntu-22-04-lts-jammy-linux](https://www.how2shout.com/linux/how-to-install-samba-on-ubuntu-22-04-lts-jammy-linux/#:~:text=Steps%20to%20install%20SAMBA%20on%20Ubuntu%2022.04%20LTS,...%206%206.%20Access%20the%20shared%20folder%20)

## Windows访问Samba共享目录

windows下可以在文件浏览器直接访问Linux主机ip查看共享的Linux目录

![image-20230130110305978](https://raw.githubusercontent.com/cursorhu/blog-images-on-picgo/master/images/202301301103020.png)

如果windows访问共享目录有权限问题（例如不能写入），需要在Linux修改共享目录的权限为可写：

```
sudo chmod -R 777 共享目录 
```

