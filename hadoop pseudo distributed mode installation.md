**hadoop pseudo distributed mode installation**

1) Skip install vmware workstation (See My Video)
2) Skip install centOS (See My Video)
3) mkdir /tools    ---->use to store some tools/softwares
4) mkdir /training   ---->use to install some software
5) install JDK 

a) then unzip and install, using follows commands:

​	tar -zvxf /tools/jdk-8u241-linux-x64.tar.gz  -C /training/

b) check java path:

echo $JAVA_HOME

6) set jdk enviroment：

a. need to configure .bash_profile file that its paths /root/:

 vi ~/.bash_profile

b. add this informations:

export JAVA_HOME=/training/jdk1.8.0_241

export JRE_HOME=$JAVA_HOME/jre

export CLASSPATH=.:$CLASSPATH:$JAVA_HOME/lib:$JRE_HOME/lib

export PATH=$PATH:$JAVA_HOME/bin:$JRE_HOME/bin;

c. make environment effects 

source ~/.bash_profile

d. check :---à java –version

7) close firewalld service : 

​	systemctl stop firewalld.service

systemctl disable firewalld.service

8) configure hostname:

hostnamectl --static set-hostname new_Hostname (you can use any HostName, I suggest use like niit1, niit2, niit111)

======================================================================

**二、****Install Hadoop****:**

9) upload hadoop-2.7.3.tar.gz to tools directory,then unzip and install:

tar -zvxf /tools/hadoop-2.7.3.tar.gz -C /training/

10) set hadoop enviroment：

a) need to configure .bash_profile file that its paths /root/:	

vi ~/.bash_profile		

b) add this informations：

export HADOOP_HOME=/training/hadoop-2.7.3

export PATH=$PATH:$HADOOP_HOME/bin:$HADOOP_HOME/sbin

c) make environment effects 

source ~/.bash_profile

d) check:

hdfs

======================================================================

**三、****Install Pseudo-Distributed Mode of hadoop**

11) configure hostname mapping ip:

​	a) check: 

​		ifconfig		

b) edit host file

vi /etc/hosts

​	a) add this information:

​		192.168.31.134 niit01(you can change as per your hostname)

12) Pseudo-Distributed Mode

​		a) mkdir /training/hadoop-2.7.3/tmp  (use to store hdfs namenode infomations)

​		b) Configure password-free login：

​		（*）ssh-keygen -t rsa   (enter 4 times)

​		（*）cd ~/.ssh/

​		（*）ssh-copy-id -i id_rsa.pub root@niit01 (change according your hostname)

​		(*) cd ~

13) Five files need to be configured：

​	a) edit hadoop-env.sh (use to set jdk ) :

​		cd /training/hadoop-2.7.3/etc/hadoop/

​		vi hadoop-env.sh

​	find JAVA_HOME and change it as per your Java Path(press / , JAVA_HOME

​	b) hdfs-site.xml  (use to set permissions and data blocks replications )

​		vi hdfs-site.xml

c) add this information inside configuration tag:

<property>

​	<name>dfs.replication</name>

​	<value>1</value>

</property>

<property>

​	<name>dfs.permissions</name>

​	<value>false</value>

</property>		

d) vi core-site.xml (use to set namenode and temporary dir)

​	<property>

​		<name>fs.defaultFS</name>

​		<value>hdfs://niit01:9000</value>

​	</property>			

​	<property>

​		<name>hadoop.tmp.dir</name>

​		<value>/training/hadoop-2.7.3/tmp</value>

​	</property>

e) vi mapred-site.xml  (use to set mapreduce's run enviroment): this file does not exist,so we need to copy

​		(*) cp /training/hadoop-2.7.3/etc/hadoop/mapred-site.xml.template /training/hadoop-2.7.3/etc/hadoop/mapred-site.xml

​		(*) vi mapred-site.xml

​		(*) add this information:

<property>	

​				<name>mapreduce.framework.name</name>

​				<value>yarn</value>

​			</property>

f) yarn-site.xml (use to set yarn) 

​		vi yarn-site.xml

​		(*) add this information:

​			<property>

​			     <name>yarn.resourcemanager.hostname</name>

​			     <value>niit01</value>

​			</property>

​			<property>

​			     <name>yarn.nodemanager.aux-services</name>

​			     <value>mapreduce_shuffle</value>

​			</property>	

14) Format NameNode:

​			hdfs namenode -format

\#notice:  if formatted is successfully, then you can see information as follows：

​	common.Storage: Storage directory /training/hadoop-2.7.3/tmp/dfs/name has been successfully formatted.

​		

15) start hadoop:

​			start-all.sh

16) visit hadoop ：

​		a) web console

​				HDFS:http://niit111:50070

​				Yarn:http://niit111:8088

​		b) use jps command to check hadoop daemons：

​			NameNode

​			DataNode

​			SecondaryNameNode

​			ReourceManager

​			NodeManager

17) if you want to stop, then you can execute：

​				stop-all.sh