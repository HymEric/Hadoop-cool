### Installation of Hive2.3.6 based on Hadoop2.6.4 on Ubuntu16.04.6 LTS

1. Have installed Hadoop2.6.4 on ubuntu16.04.6 LTS, and started.

2. Download Hive2.3.6 from [Hive-http](http://ftp.jaist.ac.jp/pub/apache/hive/)
```shell
wget http://ftp.jaist.ac.jp/pub/apache/hive/stable-2/apache-hive-2.3.6-bin.tar.gz
tart -zxvf apache-hive-2.3.6-bin.tar.gz
```
And rename it to ```hive-2.3.6```

3. Assume hive path is ```/disk2/xxx/hive-2.3.6/``` which xxx denote user name, and set hive to env path by editing ```~/.bashrc```
```shell
export HIVE_HOME=/disk2/xxx/hive-2.3.6
export PATH=$PATH:$HIVE_HOME/bin
```
And run ```source ~/.bashrc``` to make it work

4. If you haven't installed **MySql**, recommend to install mysql because it is more realiable rather than Derby. YOu can get more details in [MySql-website](https://www.mysql.com/). After installed mysql create hive database which hive will use
```shell
mysql -u root -p
create databases hive
```

5. Set Hive configuration, cd ```/disk2/xxx/hive-2.3.6/conf/```, do
```shell
cp hive-default.xml.template hive-site.xml
cp hive-env.sh.template hive-env.sh
```
- Edit hive-env.sh
```shell
export HADOOP_HOME=/disk2/xxx/hadoop
export HIVE_CONF_DIR=/disk2/xxx/hive-2.3.6/conf
export HIVE_AUX_JARS_PATH=/disk2/xxx/hive-2.3.6/lib
```
- Use mysql instead of Derby, edit related block in ```hive-site.xml```
```xml
<property>

<name>javax.jdo.option.ConnectionURL</name>

<value>jdbc:mysql://localhost:3306/hive?createDatabaseIfNotExist=true</value>

<description>JDBC connect string for a JDBC metastore</description>

</property>

<property>

<name>javax.jdo.option.ConnectionDriverName</name>

<value>com.mysql.cj.jdbc.Driver</value>

<description>Driver class name for a JDBC metastore</description>

</property>

<property>

<name>javax.jdo.option.ConnectionUserName</name>

<value>[mysql-user-name]</value>

<description>username to use against metastore database</description>

</property>

<property>

<name>javax.jdo.option.ConnectionPassword</name>

<value>[passwrord]</value>

<description>password to use against metastore database</description>

</property>
```

6. Create HDFS workspace
```shell
hadoop fs -mkdir -p /tmo
hadoop fs -mkdir -p /user/hive/warehouse
hadoop fs -chmod g+w /tmp
hadoop fs -chmod g+w /user/hive/warehouse
```

7. Initialize hive's metastore
```shell
schematool -initSchema -dbType mysql
```
If you see this, it works.
```
Starting metastore schema initialization to 2.0.0
Initialization script hive-schema-2.0.0.mysql.sql
Initialization script completed
schemaTool completed
```
8. Now you can type ```hive``` to start! And test
```shell
show databases;
```

 ### **Notes:** If your encountered some problems, bellow maybe helpful:
 1. When start hive by typing ```hive```,
 - Errors like: 
 ```
 Exception in thread "main" java.lang.IllegalArgumentException: java.net.URISyntaxException: Relative path in absolute URI: ${system:java.io.tmpdir%7D/$%7Bsystem:user.name%7D
 ```
 Replace all ```${system:java.io.tmpdir}``` with a fixed string like: ```/disk2/xxx/hive-2.3.6/tmp```
 - Errors like:
 ```
 Failed with exception java.io.IOException:java.lang.IllegalArgumentException: ja                                               va.net.URISyntaxException: Relative path in absolute URI: ${system:user.name%7D
 ```
Delete all ```system:``` in each ```${system:user.name}```

### References
- https://sjq597.github.io/2016/07/20/Ubuntu-16-04-Hive-%E6%9C%AC%E5%9C%B0%E5%AE%89%E8%A3%85%E9%85%8D%E7%BD%AE/
- https://www.zybuluo.com/hadoopMan/note/226134
- https://blog.csdn.net/zjuwwj/article/details/53766753
- [look fo more information in hive document](https://cwiki.apache.org/confluence/display/Hive/GettingStarted#GettingStarted-InstallationandConfiguration)
