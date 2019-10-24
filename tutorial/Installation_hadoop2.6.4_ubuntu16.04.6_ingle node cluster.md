###  Hadoop single node cluster: instalation of Hadoop2.6.4 on ubuntu16.04.6 LTS

1. Download Hadoop2.6.4 from [Hadoop website](https://hadoop.apache.org/)
```shell
wget https://archive.apache.org/dist/hadoop/common/hadoop-2.6.4/hadoop-2.6.4.tar.gz
```

2. Install JDK and set Java environment (maybe need sudo)
```shell
apt-get update
apt-get install default-jdk
```

3. Install SHH,rsync and sett no-password SSH
```shell
apt-get install ssh
apt-get install rsync
ssh -keygen -t dsa -P '' -f ~/.ssh/id_dsa
cat ~/.ssh/id_dsa.pub >> ~/.ssh/authorized_keys
```

4. Unzip hadoop-2.6.4.tar.gz to ~/hadoop-2.6.4/
```shell
tar -zxvf hadoop-2.6.4.tar.gz
```

5. Set hadoop environment
- edit ~/.bashrc by adding (assume hadoop path is:/disk2/xxx/hadoop-2.6.4)
```shell
export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64 # your_jdk_path
export HADOOP_HOME=/disk2/xxx/hadoop-2.6.4 # your_hadoop_path
export PATH=$PATH:$HADOOP_HOME/bin
export PATH=$PATH:$HADOOP_HOME/sbin
export HADOOP_MAPRED_HOME=$HADOOP_HOME
export HADOOP_COMMON_HOME=$HADOOP_HOME
export HADOOP_HDFS_HOME=$HADOOP_HOME
export YARN_HOME=$HADOOP_HOME

export HADOOP_COMMON_LIB_NATIVE_DIR=$HADOOP_HOME/lib/native
export HADOOP_OPTS="-DJava.library.path=$HADOOP_HOME/lib"
export JAVA_LIBRARY_PATH=$HADOOP_HOME/lib/native:$JAVA_LIBRARY_PATH
```

and run ```source ~/.bashrc``` after saving it to make it work

- ```cd hadoop-2.6.4/etc/hadoop/``` and edit hadoop-env.sh by adding
```shell
export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64
```
 (below is working in the /disk2/xxx/hadoop-2.6.4/etc/hadoop/)
 
- edit core-site.xml by adding
```xml
<configuration>
	<property>
		<name>fs.default.name</name>
		<value>hdfs://localhost:9000</value>
	</property>
</configuration>
```

- edit yarn-site.xml
```xml
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
</configuration>
```

- copy mapred-site.xml.template to mapred-site.xml and edit mapred-site.xml
```xml
<configuration>
	<property>
		<name>mapreduce.framework.name</name>
		<value>yarn</value>
	</property>

</configuration>
```

- edit hdfs-site.xml
```xml
<configuration>
		<property>
             <name>dfs.replication</name>
             <value>1</value> <!-- hadoop single node cluster -->
        </property>
        <property>
             <name>dfs.namenode.name.dir</name>
             <value>file:/disk2/xxx/hadoop-2.6.4/tmp/dfs/name</value> <!-- where to save namenode -->
        </property>
        <property>
             <name>dfs.datanode.data.dir</name>
             <value>file:/disk2/xxx/hadoop-2.6.4/tmp/dfs/data</value> <!-- where to save datanode -->
        </property>

</configuration>
```

6. Create related directory and change user belong in ```/disk2/xxx/hadoop-2.6.4``` path
```shell
mkdir -p tmp/dfs/data
mkdir -p tmp/dfs//name
chown xxx:xxx -R ../hadoop-2.6.4 # xxx is user_name
```

7. Format HDFS
```shell
hadoop namenode -format
```
after this operation, it will create ```current``` directory in ```/disk2/xxx/hadoop-2.6.4/tmp/dfs/name/``` path

8. Start HDFS and YARN
```shell
start-dfs.sh
start-yarn.sh
```
combine them in one command
```
start-all.sh
```

9. Check by run ```jps```, if the output like this then it is successfully, cool!
```shell
32386 NodeManager
32841 Jps
32219 ResourceManager
29549 NameNode
29759 DataNode
```

10. Close HDFS and YARN, you can run 
```shell
stop-dfs.sh
stop-yarn.sh
```
or in one line
```shell
stop-all.sh
```

 #### **notes:** If your encountered some problems, bellow maybe helpful:
 1. About SSH no-key, if you can not start HDFS or YARN baceuse perssion denied
 
 
 ### References
 - https://blog.csdn.net/sinat_35821976/article/details/99939757
 - https://kontext.tech/docs/DataAndBusinessIntelligence/p/default-ports-used-by-hadoop-services-hdfs-mapreduce-yarn
 - https://www.jianshu.com/p/0d4a365ef350
 - https://hadoop.apache.org
 - https://www.jianshu.com/p/67cbc4bbfd15
 - 
