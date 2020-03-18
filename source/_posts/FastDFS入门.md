---
layout: post
title:  "FastDFS 入门"
date:   2020-03-16
categories:
 - fastdfs
tags:
 - fastdfs
color: rgb(230,230,250)
cover: '../assets/blogimg/2018-10-24.png'
---
# FastDFS 简介

　　FastDFS 是一个开源的高性能分布式文件系统（DFS）。它的主要功能包括：文件存储，文件同步和文件访问，以及高容量和负载平衡。主要解决了海量数据存储问题，特别适合以中小文件（建议范围：4KB < file_size <500MB）为载体的在线服务。

　　FastDFS 系统有三个角色：跟踪服务器(Tracker Server)、存储服务器(Storage Server)和客户端(Client)

- `Tracker Server` : 跟踪服务器，主要做调度工作，起到均衡的作用；负责管理所有的 storage server 和 group，每个 storage 在启动后会连接 Tracker，告知自己所属 group 等信息，并保持周期性心跳。
- `Storage Server` : 存储服务器，主要提供容量和备份服务；以 group 为单位，每个 group 内可以有多台 storage server，数据互为备份。
- `Client` : 客户端，上传下载数据的服务器，也就是我们自己的项目所部署在的服务器。

# FastDFS 环境搭建

## 安装 libfastcommon

　　libfastcommon 是从 FastDFS 和 FastDHT 中提取出来的公共 C 函数库，基础环境，安装即可 。

　　下载并安装 libfastcommon 

```
wget https://github.com/happyfish100/libfastcommon/archive/V1.0.43.tar.gz
tar -zxvf V1.0.43.tar.gz
cd libfastcommon-1.0.43
./make.sh
./make.sh install
```

　　创建软连接

```
ln -s /usr/lib64/libfastcommon.so /usr/local/lib/libfastcommon.so
ln -s /usr/lib64/libfastcommon.so /usr/lib/libfastcommon.so
ln -s /usr/lib64/libfdfsclient.so /usr/local/lib/libfdfsclient.so
ln -s /usr/lib64/libfdfsclient.so /usr/lib/libfdfsclient.so
```

## 安装 FastDFS

　　下载并安装 FastDFS

```
wget https://github.com/happyfish100/fastdfs/archive/V6.06.tar.gz
tar -zxvf V6.06.tar.gz
cd fastdfs-6.06
./make.sh
./make.sh install
```

　　创建软连接

```
ln -s /usr/bin/fdfs_trackerd   /usr/local/bin
ln -s /usr/bin/fdfs_storaged   /usr/local/bin
ln -s /usr/bin/stop.sh         /usr/local/bin
ln -s /usr/bin/restart.sh      /usr/local/bin
```

### 配置 Tracker

　　进入 /etc/fdfs，复制 FastDFS 跟踪器样例配置文件 tracker.conf.sample，并重命名为 tracker.conf。

```
cd /etc/fdfs
cp tracker.conf.sample tracker.conf
```

　　找到 tracker.conf 中 `base_path`。创建对应目录。如：base_path = /home/yuqing/fastdfs 则执行以下命令：

```
mkdir -p /home/yuqing/fastdfs
```

　　启动 Tracker

```
service fdfs_trackerd start
```

　　查看 FastDFS Tracker 是否已成功启动 ，22122端口正在被监听，则算是Tracker服务安装成功。

```
netstat -unltp|grep fdfs
```

![](/public/doc/assets/specification/env-fastdfs/tracker.jpg)

　　关闭 Tracker

```
service fdfs_trackerd stop
```

### 配置 Storage

　　进入 /etc/fdfs 目录，复制 FastDFS 存储器样例配置文件 storage.conf.sample，并重命名为 storage.conf。 

```
cd /etc/fdfs
cp storage.conf.sample storage.conf
```

　　修改 tracker_server 的地址。如果有多个tracker 可配置多条

```
tracker_server = 192.168.16.191:22122
```

 　　启动 Storage 前确保 Tracker 是启动的。启动 Storage

```
service fdfs_storaged start
```

 　　查看 Storage 是否成功启动，23000 端口正在被监听，就算 Storage 启动成功。

```
netstat -unltp|grep fdfs
```

![](/public/doc/assets/specification/env-fastdfs/storage.jpg)

 　　关闭 Storage

```
service fdfs_storaged stop
```

 　　查看Storage和Tracker是否在通信：如显示对应的 tracker 为 ACTIVE状态，则表示正常，如下图：

![](/public/doc/assets/specification/env-fastdfs/storage-tracker-active.jpg)


### 文件上传测试

　　进入 /etc/fdfs, 复制 client.conf.sample 并重命名为 client.conf。修改 tracker_server 地址为部署的 Tracker 的地址
```
cd /etc/fdfs
cp client.conf.sample client.conf
```

　　创建一个 test.txt。

```
cp client.conf.sample test.txt
```

　　执行命令

```
/usr/bin/fdfs_upload_file /etc/fdfs/client.conf test.txt
```

　　上传成功后返回文件ID号：group1/M00/00/00/wKgz6lnduTeAMdrcAAEoRmXZPp870.txt，返回的文件 ID 由 group、存储目录、两级子目录、fileid、文件后缀名（由客户端指定，主要用于区分文件类型）拼接而成，至此 FastDFS 在 CentOS 7上安装完毕。




