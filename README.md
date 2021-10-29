# hadoop_configs
"NOTE: The below file is designed with hadoop on AWS EC2 Cloud".

The similiar file could be used for local hadoop setup wherein the below configuration: replace public dns to localhost


This repo contains the configurations file we need to add while installing hadoop installation on EC2 AWS.

Note: Replace public_dns below with the "EC2 Public DNS" value from AWS console

```
export HADOOP_HOME=/home/hadoop/hadoop
export HADOOP_INSTALL=$HADOOP_HOME
export HADOOP_MAPRED_HOME=$HADOOP_HOME
export HADOOP_COMMON_HOME=$HADOOP_HOME
export HADOOP_HDFS_HOME=$HADOOP_HOME
export YARN_HOME=$HADOOP_HOME
export HADOOP_COMMON_LIB_NATIVE_DIR=$HADOOP_HOME/lib/native
export PATH=$PATH:$HADOOP_HOME/sbin:$HADOOP_HOME/bin
export JAVA_HOME=/usr/lib/jvm/java-1.8.0-openjdk-amd64

```



(1) Download hadoop from below url:

wget https://archive.apache.org/dist/hadoop/common/hadoop-3.1.0/hadoop-3.1.0.tar.gz

(2) Download JAVA 8

sudo apt-get install openjdk-8-jdk

(3) Update required below files in etc/hadoop

- core-site.xml

```
<configuration>
<property>
  <name>fs.defaultFS</name>
  <value>hdfs://<public dns>:9000</value>
</property>
</configuration>
```


- hdfs-site.xml

```
<configuration>
<property>
 <name>dfs.replication</name>
 <value>1</value>
</property>

<property>
  <name>dfs.namenode.name.dir</name>
    <value>file:///home/hadoop/hadoop/hadoopdata/hdfs/namenode</value>
</property>

<property>
  <name>dfs.datanode.data.dir</name>
    <value>file:///home/hadoop/hadoop/hadoopdata/hdfs/datanode</value>
</property>
</configuration>
```



-yarn-site.xml

```
<configuration>
 <property>
  <name>yarn.nodemanager.aux-services</name>
    <value>mapreduce_shuffle</value>
 </property>

  <property>
          <name>yarn.nodemanager.auxservices.mapreduce.shuffle.class</name>
    <value>org.apache.hadoop.mapred.ShuffleHandler</value>
  </property>
  <property>
          <name>yarn.resourcemanager.hostname</name>
    <value><public dns></value>
  </property>
</configuration>

```

-mapred-site.xml


```
<configuration>
 <property>
  <name>mapreduce.framework.name</name>
   <value>yarn</value>
 </property>
   <property>
    <name>mapreduce.jobtracker.address</name>
    <value><public dns>:8021</value>
  </property>
</configuration>
```
NOTE:
If you get "JAVA_HOME not defined error", open hadoop-env.sh file
and search for export JAVA_HOME, replace it will export JAVA_HOME=/usr/lib/jvm/java-1.8.0-openjdk-amd64


(4) Update /etc/hosts

hostname             public dns



(5) Execute hadoop commands
```
 hdfs namenode -format

./start-all.sh
```


Note:
If you encounter errors like "Connection Refused - Public Key", please execute the below command from your hadoop user (NOT ROOT USER):
```
ssh-keygen -t rsa
```
```
cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys
```
```
chmod og-wx ~/.ssh/authorized_keys
```



