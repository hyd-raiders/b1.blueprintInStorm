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

## 以下命令请自觉理解
 hadoop checknative -a
 

