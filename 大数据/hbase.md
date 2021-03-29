# HBase

## 概览

### Hbase是什么

HBase 基于 Google的BigTable论文而来，是一个**分布式海量列式非关系**型**数据库系统**，可以提供超大规模数据集的实时随机读写。

**HBase适合海量明细数据的存储，并且后期需要有很好的查询性能(单表超千万、上亿，且并发要求高)**

列式存储优点：

- 减少储存空间
- 支持很多列

### 特点

- 海量存储：底层基于HDFS存储海量数据。
- 列式存储：HBase表的数据是基于列族进行存储的，一个列族包含若干列。
- 极易扩展：底层依赖HDFS，当磁盘空间不足时，动态扩展DataNode服务节点。
- 高并发：支持高并发读写请求。
- 稀疏：主要针对HBase列的灵活性，在列族中，可以指定任意多的列，在列数据为空的情况下，是不会占用存储空间的。
- 数据的多版本：HBase表中的数据可以有多个版本值，版本号为插入数据时的时间戳。
- 数据类型单一：所有数据在HBase中都以字节数组进行存储。

## 安装

1. 下载安装包

    http://archive.apache.org/dist/hbase/1.3.1/

   hbase-1.3.1-bin.tar.gz

2. 上传安装包并解压

   ```shell
   scp hbase-1.3.1-bin.tar.gz root@hadoop1:/opt/lagou/software
   tar -zxvf hbase-1.3.1-bin.tar.gz -C /opt/lagou/servers
   ```

3. 添加hadoop配置

   ```shell
   ln -s /opt/lagou/servers/hadoop-2.9.2/etc/hadoop/core-site.xml /opt/lagou/servers/hbase-1.3.1/conf/core-site.xml
   ln -s /opt/lagou/servers/hadoop-2.9.2/etc/hadoop/hdfs-site.xml /opt/lagou/servers/hbase-1.3.1/conf/hdfs-site.xml
   ```

4.  vim hbase-env.sh

   ```shell
   # 添加java环境变量量
   export JAVA_HOME=/opt/module/jdk1.8.0_231
   # 指定使用用外部的zk集群
   export HBASE_MANAGES_ZK=FALSE
   ```

5.  vim hbase-site.xml

   ```xml
   <configuration>
       <!-- 指定hbase在HDFS上存储的路路径 -->
       <property>
           <name>hbase.rootdir</name>
           <value>hdfs://hadoop1:9000/hbase</value>
       </property>
       <!-- 指定hbase是分布式的 -->
       <property>
           <name>hbase.cluster.distributed</name>
           <value>true</value>
       </property>
       <!-- 指定zk的地址,多个用用“,”分割 -->
       <property>
           <name>hbase.zookeeper.quorum</name>
           <value>hadoop1:2181,hadoop2:2181,hadoop3:2181</value>
       </property>
   </configuration>
   ```

6. vim regionservers

   ```tex
   hadoop1
   hadoop2
   hadoop3
   ```

7. hbase的conf目录下  vim backup-masters  (Standby Master)

   ```tex
   hadoop2
   ```

8. 分发

   ```shell
   send_rsync hbase-1.3.1
   ```

9. vim /etc/profile (所有节点修改)

   ```shell
   export HBASE_HOME=/opt/lagou/servers/hbase-1.3.1
   export PATH=$PATH:$HBASE_HOME/bin
   ```

10. 启动

    ```shell
    start-hbase.sh
    stop-hbase.sh
    ```

    打开管理端网页

    ```tex
    http://hadoop1:16010
    ```

## 协处理器

### 代码编写

### 关联至表

#### 打包jar并上传

```shell
cd /opt/lagou/softwares
mv original-hbaseStudy-1.0-SNAPSHOT.jar processor.jar
hdfs dfs -mkdir -p /processor
hdfs dfs -put processor.jar /processor
```

#### 挂载

```shell
describe 'homework_friend'
# 1001表示优先级，该数字越小，优先级越高
alter 'homework_friend',METHOD => 'table_att','Coprocessor'=>'hdfs://hadoop1:9000/processor/processor.jar|priv.lagou.homework.DeleteFriendProcessor|1001|'
```

```xml
<property>
	<name>hbase.table.sanity.checks</name>
	<value>false</value>
</property>
```

#### 卸载

```shell
disable 'homework_friend'
alter 'homework_friend',METHOD=>'table_att_unset',NAME=>'coprocessor$1'
enable 'homework_friend'
```











































