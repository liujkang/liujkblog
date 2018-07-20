---
title: storm集群安装步骤
categories:  # 这里写的分类会自动汇集到 categories 页面上，分类可以多级
- 技术开发 
tags:   # 这里写的标签会自动汇集到 tags 页面上
- 大数据
- storm
---

通过阅读此文档，快速完成storm集群的安装。

<!-- more -->

>一、环境准备

1. storm官方地址[：http://storm.apache.org/index.html](http://storm.apache.org/index.html)
2. 下载 [http://storm.apache.org/downloads.html](http://storm.apache.org/downloads.html) 本文版本(apache-storm-1.1.1.tar.gz)

>二、安装过程

1. 安装zk集群(此处不做详细说明，具体zk安装请去参考zk安装相关文档)
2. 解压安装文件，进入==conf/storm.yaml==,配置

```
storm.zookeeper.servers:
  - "111.222.333.444"
  - "555.666.777.888"

storm.local.dir: "/mnt/storm"

nimbus.seeds: ["111.222.333.44"]

supervisor.slots.ports:(选配)
    - 6700
    - 6701
    - 6702
    - 6703
```
3. 将配置好的storm安装文件，分发到集群中的集群上，scp -r xxxxx  root@xx.xxx.xx.xx:/目录名称 
4. 启动命令

```
启动管理节点
  -storm.sh nimbus &
启动工作节点
  -storm.sh supervisor &
启动管理界面
  -storm.sh ui &
```
5. 管理界面，默认端口8080

```
 访问：storm.sh ui所在机器的ip地址 http://ip:8080
```


