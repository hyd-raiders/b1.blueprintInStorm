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
hadoopSimple 192.168.0.221   centos7.4 
4 cores 4 G

2. hadoop cluster
hadoopSimple master 

HDFS： NameNode SecondaryNameNode DataNode
YARN: ResourceManager NodeManager WebAppProxy
MapReduce: MapReduce Job History Server (optional)

## 安装部署
### 基础环境
yum install -y vim gcc cc lrzsz wget net-tools curl tree
#java
#不要用openjdk openjdk无法实用这里
#yum install -y java-1.8.0-openjdk*
cd /data
mkdir -p /usr/lib/jvm
tar -zxvf jdk-8u45-linux-x64.tar.gz -C /usr/lib/jvm

vim /etc/profile 
结尾加上：
export JAVA_HOME=/usr/lib/jvm/jdk1.8.0_45
export JRE_HOME=${JAVA_HOME}/jre
export CLASSPATH=.:${JAVA_HOME}/lib:${JRE_HOME}/lib
export  PATH=${JAVA_HOME}/bin:$PATH


source /etc/profile

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

echo "export HADOOP_HOME=/data/hadoop" >> /etc/profile
source /etc/profile

#用户
vim ${HADOOP_HOME}/sbin/start-dfs.sh 
vim ${HADOOP_HOME}/sbin/stop-dfs.sh 
在顶部空白处添加内容： 
HDFS_DATANODE_USER=root 
HADOOP_SECURE_DN_USER=hdfs 
HDFS_NAMENODE_USER=root 
HDFS_SECONDARYNAMENODE_USER=root 

vim ${HADOOP_HOME}/sbin/start-yarn.sh 
vim ${HADOOP_HOME}/sbin/stop-yarn.sh
在顶部空白处添加内容： 
YARN_RESOURCEMANAGER_USER=root
HADOOP_SECURE_DN_USER=yarn
YARN_NODEMANAGER_USER=root


#JAVA_HOME
echo $JAVA_HOME
vim ${HADOOP_HOME}/etc/hadoop/hadoop-env.sh
修改JAVA_HOME为真实路径


#配置
mkdir -p /data/hdfs/tmp
vim ${HADOOP_HOME}/etc/hadoop/core-site.xml

    <!--指定namenode的地址-->
    <property>
        <name>fs.defaultFS</name>
        <value>hdfs://192.168.0.221:9000</value>
    </property>
    <!--用来指定使用hadoop时产生文件的存放目录-->
    <property>
        <name>hadoop.tmp.dir</name>
        <value>file:/data/hdfs/tmp</value> 
    </property>
    <!--用来设置检查点备份日志的最长时间-->
    <property>
        <name>fs.checkpoint.period</name>
        <value>3600</value> 
    </property>

mkdir -p /data/hdfs/namenode
mkdir -p /data/hdfs/datanode
vim ${HADOOP_HOME}/etc/hadoop/hdfs-site.xml
<!--指定hdfs保存数据的副本数量-->
    <property>
        <name>dfs.replication</name>
        <value>2</value>
    </property>
    <!--指定hdfs中namenode的存储位置-->
    <property>
        <name>dfs.namenode.name.dir</name> 
        <value>file:/data/hdfs/namenode</value>
    </property>
    <!--指定hdfs中datanode的存储位置-->
    <property>
        <name>dfs.datanode.data.dir</name>
        <value>file:/data/hdfs/datanode</value>
    </property>

vim ${HADOOP_HOME}/etc/hadoop/mapred-site.xml

    <property>
        <name>mapreduce.framework.name</name>
        <value>yarn</value>
    </property>

vim ${HADOOP_HOME}/etc/hadoop/yarn-site.xml
..


### startup

#format
$HADOOP_HOME/bin/hdfs namenode -format hSimple

#hdfs
$HADOOP_HOME/bin/hdfs --daemon start namenode
$HADOOP_HOME/bin/hdfs --daemon start datanode
or
$HADOOP_HOME/sbin/start-dfs.sh
#yarn
$HADOOP_HOME/bin/yarn --daemon start resourcemanager
$HADOOP_HOME/bin/yarn --daemon start nodemanager
$HADOOP_HOME/bin/yarn --daemon start proxyserver
OR
$HADOOP_HOME/sbin/start-yarn.sh
#mr history
$HADOOP_HOME/bin/mapred --daemon start historyserver

#all 
$HADOOP_HOME/sbin/start-dfs.sh
$HADOOP_HOME/sbin/start-yarn.sh
$HADOOP_HOME/bin/mapred --daemon start historyserver

#check 
jps
like:
    18481 JobHistoryServer
    18267 NodeManager
    17485 DataNode
    17693 SecondaryNameNode
    17951 ResourceManager
    18527 Jps

访问web页面：
NameNode	http://nn_host:port/	Default HTTP port is 9870.
ResourceManager	http://rm_host:port/	Default HTTP port is 8088.
MapReduce JobHistory Server	http://jhs_host:port/	Default HTTP port is 19888.

查看生成的文件
tree /data/hdfs


### stop
$HADOOP_HOME/bin/hdfs --daemon stop namenode
$HADOOP_HOME/bin/hdfs --daemon stop datanode
or
$HADOOP_HOME/sbin/stop-dfs.sh

$HADOOP_HOME/bin/yarn --daemon stop resourcemanager
$HADOOP_HOME/bin/yarn --daemon stop nodemanager
or
$HADOOP_HOME/sbin/stop-yarn.sh

$HADOOP_HOME/bin/yarn stop proxyserver
$HADOOP_HOME/bin/mapred --daemon stop historyserver

#all 
$HADOOP_HOME/sbin/stop-dfs.sh
$HADOOP_HOME/sbin/stop-yarn.sh
$HADOOP_HOME/bin/yarn stop proxyserver
$HADOOP_HOME/bin/mapred --daemon stop historyserver