## hadoop的官网
[hadoop官网](http://hadoop.apache.org/)

## 下载并安装hadoop（本人使用的是2.10.1）

 1. 下载：[hadoop2.10.1](https://download.csdn.net/download/u011767319/12918330)
 2. 安装：`tar -zxvf 解压文件`
 3. 在linux 环境下也可以 直接用 wget http地址 的形式下载

## 配置hadoop及启动

 - 集群最好改动一下hadoop的java配置地址：准备启动 Hadoop 群集，解包下载的 Hadoop 分发。在分发中，编辑文件etc/hadoop/hadoop-env.sh以定义一些参数，如下所示

	```java
	# set to the root of your Java installation
	export JAVA_HOME=/usr/java/latest
	```

 - 单个机子部署（使用伪分布式）配置：
 	修改etc/hadoop/core-site.xml 文件
	```java
	<configuration>
		#配置成hdfs模式
	    <property>
	        <name>fs.defaultFS</name>
	        <value>hdfs://localhost:9000</value>
	    </property>
	    #将生产模式的运行日志生成到指定位置
	    <property>
                <name>hadoop.tmp.dir</name>
                <value>/opt/hadoop/hadoop-2.10.1/data/tmp</value>
        </property>
	</configuration>
	```
	修改etc/hadoop/hdfs-site.xml 文件
	```java
	<configuration>
	    <property>
	        <name>dfs.replication</name>
	        <value>1</value>
	    </property>
	</configuration>
	```

 - 第一次启动namenode 一定要格式化（只在第一次启动时需要格式化，其他情况不需要）
	```java
	bin/hdfs namenode -format
	```

 - 启动namenode
	```java
	#启动
	sbin/hadoop-daemon.sh start namenode
	#停止
	sbin/hadoop-daemon.sh stop namenode
	```

 - 启动datanode
	```java
	#启动
	sbin/hadoop-daemon.sh start datanode
	#停止
	sbin/hadoop-daemon.sh stop datanode
	```

 - 启动好之后可以通过`服务器ip:50070`查看情况
 - 创建文件（需要配置使用hdfs才能用）
	```java
	bin/hdfs dfs -mkdir /user
	bin/hdfs dfs -mkdir /user/<username>
	#将输入文件复制到分布式文件系统中
	bin/hdfs dfs -mkdir input
	bin/hdfs dfs -put etc/hadoop/*.xml input
	#运行提供的一些示例
	bin/hadoop jar share/hadoop/mapreduce/hadoop-mapreduce-examples-3.2.1.jar grep input output 'dfs[a-z.]+'
	#检查输出文件：将输出文件从分布式文件系统复制到本地文件系统并检查它们
	bin/hdfs dfs -get output output
	cat output/*
	#或者
	bin/hdfs dfs -cat output/*
	```
**配置下hadoop的环境**

```java
vim /etc/profile

#hadoop
export HADOOP_HOME=/home/wj/hadoop2.7.7
export PATH=$HADOOP_HOME/bin:$HADOOP_HOME/sbin:$PATH

source /etc/profile
```
## 安装hive
![在这里插入图片描述](https://img-blog.csdnimg.cn/20201122195325210.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTE3NjczMTk=,size_16,color_FFFFFF,t_70#pic_center)
官网下载相应的包

配置环境（一定要先配置好 hadoop的环境）

```java
vim /etc/profile

#hive  
export HIVE_HOME=/usr/local/hive
export PATH=$PATH:$HIVE_HOME/bin

source /etc/profile
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/20201122195538823.png#pic_center)
**建立Hive专用的元数据库**

> 在Hive的conf目录下的文件“hive-site.xml”中增加如下配置
> 原目录没有，可以vim 创建一个hive-site.xml

```java
<configuration>
<property>
    <name>javax.jdo.option.ConnectionURL</name>
    <value>jdbc:mysql://gz-cdb-73kggk47.sql.tencentcdb.com:63524/hive?createDatabaseIfNotExist=true</value>
</property>
<property>
    <name>javax.jdo.option.ConnectionDriverName</name>
    <value>com.mysql.jdbc.Driver</value>
</property>
<property>
    <name>javax.jdo.option.ConnectionUserName</name>
    <value>root</value>
</property>
<property>
    <name>javax.jdo.option.ConnectionPassword</name>
    <value>ccnfgame001</value>
</property>
</configuration>
```
**把MySQL的JDBC驱动包复制到Hive的lib目录下（略，下载地址：https://dev.mysql.com/downloads/connector/j/）**

> （驱动包名为：mysql-connector-java-5.1.46-bin.jar）看自己数据库什么版本，就下载什么版本就行了

**检查连接 在bin目录下运行以下代码**

```java
./schematool -initSchema -dbType mysql
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20201123103735723.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTE3NjczMTk=,size_16,color_FFFFFF,t_70#pic_center)
**hive 远程服务 (端口号10000) 启动方式 （Thrift服务）**

```java
bin/hive –service hiveserver2 &（&表示后台运行）
用java，python等程序实现通过jdbc等驱动的访问hive就用这种起动方式了，这个是程序员最需要的方式了
```

[参考文章](https://www.cnblogs.com/netuml/p/7841387.html)

## 安装spark 
官网下载相应的包

scala

配置环境（一定要先配置好 hadoop的环境）
```java
vim /etc/profile

#scala
export SCALA_HOME=/home/ubuntu/ljq/hadoopAll/spark/scala-2.12.12
export PATH=$PATH:$SCALA_HOME/bin


source /etc/profile
```
[参考地址](https://www.cnblogs.com/swordfall/p/7903678.html)
