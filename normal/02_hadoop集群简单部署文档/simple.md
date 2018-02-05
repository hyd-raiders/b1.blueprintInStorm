# hadoop集群简单部署文档

hadoop 3.0.0 cluster on single centos7.4
no HA


## 目标
    简单部署hadoop集群，不包括ha等内容，但支持多处理节点，基于该文档，我们可以了解hadoop的简单工作过程、并把开发环境搭建起来

    该文档纯粹是用于熟悉如何部署hadoop，没有实用价值

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

HDFS： NameNode SecondaryNameNode DataNode
YARN: ResourceManager NodeManager WebAppProxy
MapReduce: MapReduce Job History Server (optional)

## 安装部署
### 基础环境
yum install -y vim gcc cc lrzsz wget net-tools curl
#java
yum install -y java-1.8.0-openjdk*
java -version
### 防火墙
### 时间同步
### 组件下载
mkdir -p /data
cd /data
wget -C http://mirror.bit.edu.cn/apache/hadoop/common/hadoop-3.0.0/hadoop-3.0.0.tar.gz

### ssh 免密登录
vim /etc/ssh/sshd_config
#去除注释---------------------------------
RSAAuthentication yes
PubkeyAuthentication yes
#---------------------------------
#生成秘钥 均采用默认配置
ssh-keygen -t rsa
#放入公钥文件夹
cp /root/.ssh/id_rsa.pub /root/.ssh/authorized_keys
#test
ssh localhost

### 安装
cd /data
tar xvzf hadoop-3.0.0.tar.gz
mv hadoop-3.0.0 hadoop


### HDFS配置

echo "export JAVA_HOME=/usr/lib/jvm/java-1.8.0-openjdk-1.8.0.161-0.b14.el7_4.x86_64" >> /etc/profile
echo "export HADOOP_HOME=/data/hadoop" >> /etc/profile
source /etc/profile

#配置


### startup

？？？？ 
$HADOOP_HOME/bin/hdfs namenode -format hSimple
$HADOOP_HOME/bin/hdfs --daemon start namenode
$HADOOP_HOME/bin/hdfs --daemon start datanode
$HADOOP_HOME/bin/yarn --daemon start resourcemanager
$HADOOP_HOME/bin/yarn --daemon start nodemanager
$HADOOP_HOME/bin/yarn --daemon start proxyserver