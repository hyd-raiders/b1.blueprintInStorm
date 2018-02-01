# hadoop集群简单部署文档

hadoop 3.0.0 cluster on centos7.4

## 目标
    简单部署hadoop集群，不包括ha等内容，但支持多处理节点，基于该文档，我们可以了解hadoop的简单工作过程、并把开发环境搭建起来
## 官网路径
http://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/ClusterSetup.html

## before of all
    你需要了解以下概念
    journalnode


## 环境规划
1. 主机集群
hadoop1 192.168.1.101   centos7.4
hadoop2 192.168.1.102   centos7.4
hadoop3 192.168.1.103   centos7.4

2. hadoop cluster
hadoop1 master 
hadoop2 slave
hadoop3 slave

## 安装部署
### 基础环境
yum install -y vim gcc cc lrzsz wget net-tools curl

### 防火墙

### 时间同步

### 组件下载

