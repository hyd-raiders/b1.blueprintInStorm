# hadoop 常用命令或者使用

## 阅读顺序


### hadoop 常用命令
命令格式:
shellcommand [SHELL_OPTIONS] [COMMAND] [GENERIC_OPTIONS] [COMMAND_OPTIONS]
具体解释详见官方文档中 Hadoop Commands Guide

you can read 《commonCmds.md》

### hdfs概念
HDFS Federation
ViewFs 
HDFS snapshot

Offline Edits Viewer：
ls /data/hdfs/namenode/current/
 hdfs oev -i hdfs/namenode/current/edits_0000000000000000001-0000000000000000008 -o edits.xml
 vim edits.xml

 Offline Image Viewer
 Centralized Cache Management

 and so on

 ### mapreduce