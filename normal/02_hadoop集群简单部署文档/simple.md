# hadoop集群简单部署文档

hadoop 3.0.0 cluster on single centos7.4
no HA


## 目标
    简单部署hadoop集群，不包括ha等内容，但支持多处理节点，基于该文档，我们可以了解hadoop的简单工作过程、并把开发环境搭建起来
## 官网路径
http://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/ClusterSetup.html

## before of all
    你需要了解以下概念
    journalnode


## 环境规划
1. 主机集群
hadoopSimple 192.168.0.100   centos7.4 
4 cores 4 G

2. hadoop cluster
hadoopSimple master 

## 安装部署
### 基础环境
yum install -y vim gcc cc lrzsz wget net-tools curl

### 防火墙

### 时间同步

### 组件下载

