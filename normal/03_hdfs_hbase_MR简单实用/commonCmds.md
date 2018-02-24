# 简介
本文介绍常用的hadoop命令

# 前置条件
1. hadoop环境, 最简单的环境如02/simple.md
2. 开启各种服务


# follow me
 vim /etc/profile
#++++++++++++++++++++++++++++++++++++++
#前提是HADOOP_HOME 已经配置
export PATH=${HADOOP_HOME}/bin:$PATH
#++++++++++++++++++++++++++++++++++++++
source /etc/profile
#test
hadoop

## 设置别名
#根据实际情况设置别名 hadoop 太长容易打错
echo "alias hd='hadoop'" >> /etc/profile
echo "alias hf='hadoop -fs'" >> /etc/profile
source /etc/profile
 
## 以下命令请自行理解
 hadoop checknative -a
 
 mkdir test
 cd test

 cat >> 1.txt <<EOF
 test
 EOF

hadoop fs -appendToFile 1.txt 1.txt
hadoop fs -cat 1.txt
hadoop fs -appendToFile 1.txt 1.txt
hadoop fs -cat 1.txt
hadoop fs -cat file:///root/test/1.txt

hadoop fs -copyFromLocal 1.txt 3.txt
hadoop fs -cat 3.txt

hadoop fs -copyToLocal 2.txt 3.txt
cat 3.txt

hadoop fs -count 1.txt
hadoop fs -count /

hadoop fs -ls /
hadoop fs -ls /usr/root

hadoop fs -checksum 1.txt

hadoop fs -cp 1.txt cp.txt
hadoop fs -cat cp.txt

 hadoop fs -df

 hadoop fs -du -h  /user

hadoop fs -find / -name 1.txt

hadoop fs -get 1.txt 8.txt
cat 8.txt

hadoop fs -getfacl ./

hf -getfattr -d 1.txt

hf -help rm

hf -help ls

hf -help moveFromLocal
hf -help moveToLocal

hf -tail 1.txt

hf -text 1.txt

hf -touchz 1.txt

hf -help put

hdfs dfs .. (just like hadoop fs)

hdfs envvars

 hdfs getconf -namenodes


 ## 常用命令