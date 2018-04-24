Centos7搭建Hadoop2.7.5

（一）机器准备
1.三台机器安装centos7,并用桥接方式连接网络，并分配三台机器的IP如下:
    192.168.1.131 、192.168.1.132、192.168.1.133
2.分别设置各台机的hostname为hadoopMaster、hadoopSlave1、hadoopSlave2
    hostnamectl set-hostname hadoopMaster
3.分别修改三台机器的/etc/hosts（内容都一样）
    vim /etc/hosts

（二）安装包准备
    hadoop-2.7.5.tar.gz
    jdk-8u161-linux-x64.tar.gz
（三）安装步骤
1.在三台机分别创建相同用户：hadoopUser,并输入密码为123456
    useradd hadoopUser
    passwd hadoopUser
2.关闭防火墙
    关闭防火墙：systemctl stop firewalld.service
    禁用防火墙：systemctl disable firewalld.service
    查看防火墙状态：firewall-cmd --state
    重启：reboot
3.为每台机的用户hadoopUser配置ssh  (要相同的用户，因为ssh通信默认使用相同用户身份访问另一台机子）
    （1）root用户下修改：vim /etc/ssh/sshd_config,
           设置这三项后，执行：service sshd restart

    （2）退出root，切换用户hadoopUser：su - hadoopUser
    （3）在hadoopUser用户下操作（在三台机都执行相同的操作）
            生成密钥对： ssh-keygen -t dsa（一路回车即可）
            转入ssh目录下：cd .ssh
            导入公钥：cat id_dsa.pub >>  authorized_keys
            修改authorized_keys权限：chmod 644 authorized_keys 
            验证：ssh hadoopMaster（其余两台为hadoopSlave1、hadoopSlave2）
    （4）在hadoopMaster机器下的hadoopUser用户下操作：
            cat ~/.ssh/id_dsa.pub | ssh hadoopUser@hadoopSlave1 'cat - >> ~/.ssh/authorized_keys'      
            cat ~/.ssh/id_dsa.pub | ssh hadoopUser@hadoopSlave2 'cat - >> ~/.ssh/authorized_keys '  
            中间会输入两台机器的密码
            验证是否成功： ssh  hadoopSlave2 （成功登陆hadoopSlave2机器）
4.安装hadoop
     4.1 在主机上安装hadoop
        （1）rz 选择hadoop-2.7.5.tar.gz
        （2）解压hadoop至usr下，并重名名为hadoop
                tar zxvf  /home/hadoop-2.7.5.tar.gz -C /usr/
                cd /usr/
                mv hadoop-2.7.5 hadoop
        （3）授权给用户hadoopUser  
                chown -R hadoopUser /usr/hadoop
     4.2 在每台机上创建hdfs相关文件夹
        （1）创建hadoop数据文件的目录
            mkdir /home/hadoopdir
        （2）创建hadoop的临时目录
            mkdir /home/hadoopdir/tmp
        （3）创建hdfs目录
            mkdir /home/hadoopdir/hdfs
        （4）创建hdfs下的datanode目录
            mkdir /home/hadoopdir/hdfs/data
        （4）创建hdfs下的namenode目录
            mkdir /home/hadoopdir/hdfs/name
        （5）授权给用户hadoopUser  
            chown -R hadoopUser /home/hadoopdir
     4.3 在每台机上安装JDK
             #下载jdk1.8 jdk-xx.tar.gz
            cd /home/
            mkdir data
            cd data
            tar -zxvf jdk-xx.tar.gz
            cd /usr/
            mkdir java
            mv /home/data/jdk-xx /usr/java/
            rm -rf /home/data/jdk-xx.tar.gz
     4.4 在每台机上配置环境变量
            vim  /etc/profile
            在最下面增加以下内容：
            # hadoop config begin
            export HADOOP_INSTALL=/usr/hadoop
            # hadoop config end
            # jdk config begin
            export JAVA_HOME=/usr/java/jdk1.8.0_151
            export JRE_HOME=$JAVA_HOME/jre
            export PATH=$PATH:$JAVA_HOME/bin:$JRE_HOME/bin:$HADOOP_INSTALL/bin:$HADOOP_INSTALL/sbin
            export CLASSPATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar:$JRE_HOME/lib
            #jdk config end
            使环境变量生效：
            source /etc/profile
     4.5 在主机上验证
            java -version
            hadoop version
5.配置hadoop
        cd /usr/hadoop/etc/hadoop/
      5.1 修改主机hadoop-env.sh（/usr/hadoop/etc/hadoop/hadoop-env.sh）
        vim hadoop-env.sh
          修改内容为:
        # The java implementation to use.
        export JAVA_HOME=/usr/java/jdk1.8.0_161
      5.2 修改core-site.xml（/usr/hadoop/etc/hadoop/core-site.xml）
        vim core-site.xml
        <configuration>
       <property>
                <name>fs.defaultFS</name>
                <value>hdfs://linuxmaster:9000</value>
       </property>
       <property>
                <name>io.file.buffer.size</name>
                <value>131072</value>
        </property>
       <property>
               <name>hadoop.tmp.dir</name>
               <value>file:/home/hadoopdir/tmp/</value>
               <description>A base for other temporary   directories.</description>
       </property>
        <property>
               <name>hadoop.proxyuser.hadoop.hosts</name>
               <value>*</value>
       </property>
       <property>
               <name>hadoop.proxyuser.hadoop.groups</name>
               <value>*</value>
       </property>
</configuration>
      5.3 修改hdfs-site.xml（/usr/hadoop/etc/hadoop/hdfs-site.xml）
        vim hdfs-site.xml
<configuration>
      <property>
             <name>dfs.namenode.name.dir</name>
             <value>file:///home/hadoopdir/hdfs/name</value>
       </property>
       <property>
              <name>dfs.datanode.data.dir</name>
              <value>file:///home/hadoopdir/hdfs/data</value>
       </property>
       <property>
               <name>dfs.replication</name>
               <value>2</value>
        </property>
        <property>
                <name>dfs.webhdfs.enabled</name>
                <value>true</value>
         </property>
</configuration>
      5.4 修改mapred-site.xml（/usr/hadoop/etc/hadoop/mapred-site.xml）
        cp mapred-site.xml.template mapred-site.xml
        vim mapred-site.xml
<configuration>         
    <property> 
      		<name>mapreduce.framework.name</name>
          	<value>yarn</value>
       </property>
       <property>
                 <name>mapreduce.jobhistory.address</name>
                 <value>linuxmaster:10020</value>
        </property>
        <property>
        	 <name>mapreduce.jobhistory.webapp.address</name>
                 <value>linuxmaster:19888</value>
       	</property>
 	<property>
                 <name>mapreduce.jobtracker.http.address</name>
                 <value>linuxmaster:50030</value>
       	</property>
 	<property>
                 <name>mapred.job.tracker</name>
                 <value>linuxmaster:9001</value>
       </property>
</configuration>
      5.5 修改 yarn-site.xml（/usr/hadoop/etc/hadoop/yarn-site.xml）
        vim yarn-site.xml
<configuration>
<!-- Site specific YARN configuration properties -->
        <property>
               <name>yarn.nodemanager.aux-services</name>
               <value>mapreduce_shuffle</value>
        </property>
        <property>                                                               
             <name>yarn.nodemanager.aux-services.mapreduce.shuffle.class</name>
                <value>org.apache.hadoop.mapred.ShuffleHandler</value>
        </property>
       <property>
                <name>yarn.resourcemanager.hostname</name>
                <value>linuxmaster</value>
         </property>
         <property>
                <name>yarn.resourcemanager.address</name>
                <value>linuxmaster:8032</value>
          </property>
          <property>
                 <name>yarn.resourcemanager.scheduler.address</name>
                 <value>linuxmaster:8030</value>
          </property>
          <property>
                 <name>yarn.resourcemanager.resource-tracker.address</name>
                 <value>linuxmaster:8031</value>
          </property>
      	  <property>
                 <name>yarn.resourcemanager.admin.address</name>
                 <value>linuxmaster:8033</value>
          </property>
          <property>
                  <name>yarn.resourcemanager.webapp.address</name>
                  <value>linuxmaster:8088</value>
          </property>
</configuration>
    5.6 修改 slaves文件（/usr/hadoop/etc/hadoop/slaves）
        vim slaves
        内容为：
        linuxslave01
        linuxslave02
6.搭建hadoop集群
    （1）用普通用户hadoopUser登录主机
            su - hadoopUser
    （2）格式化hadoop文件
            hadoop  namenode -format   (最后出现“util.ExitUtil: Exiting with status 0”，表示成功)
    （3）发送hdfs内容给从节点
            scp  -r /home/hadoopdir/hdfs/*  linuxslave01:/home/hadoopdir/hdfs
            scp  -r /home/hadoopdir/hdfs/*  linuxslave02:/home/hadoopdir/hdfs
    （4）发送hadoop内容给从节点
            scp -r /usr/hadoop/*  linuxslave01:/usr/hadoop/　　
            scp -r /usr/hadoop/*  linuxslave02:/usr/hadoop/
7.启动hadoop集群
    （1）启动集群
        /usr/hadoop/sbin/start-all.sh
    （2）查验集群
        在主节点和从节点输入:jps 分别进行查看
    （3）网页查看
        http://linuxmaster:50070  (查看live node)    
        http://linuxmaster:8088（查看yarn环境）
        可输入linuxmaster对应的ip进行查看
        