---
title: hbase
tags: hbase
keywords: hbase
abbrlink: 8e90eaec
date: 2022-09-16 15:07:49
---

抄自[https://www.cnblogs.com/yy-yang/p/14677554.html](https://www.cnblogs.com/yy-yang/p/14677554.html)

<!-- more -->

## **zookeeper安装**

### 通过镜像下载

[https://mirrors.tuna.tsinghua.edu.cn/apache/zookeeper/zookeeper-3.8.0/apache-zookeeper-3.8.0-bin.tar.gz](https://mirrors.tuna.tsinghua.edu.cn/apache/zookeeper/zookeeper-3.8.0/apache-zookeeper-3.8.0-bin.tar.gz)

解压

```bash
tar -zxvf apache-zookeeper-3.8.0-bin.tar.gz -C /opt/
```

### 环境变量

```bash
nano /etc/profile
export ZOOKEEPER_HOME=/opt/apache-zookeeper-3.8.0-bin
export PATH=$PATH:$ZOOKEEPER_HOME/bin
```

刷新

`source /etc/profile`

### 修改配置文件

```bash
cd /opt/apache-zookeeper-3.8.0-bin/conf
cp zoo_sample.cfg zoo.cfg
```

`nano zoo.cfg`

增加以下内容

```bash
dataDir=/opt/apache-zookeeper-3.8.0-bin/data
server.0=master:2888:3888
server.1=slave1:2888:3888
server.2=slave2:2888:3888
```

创建文件夹

```bash
mkdir /opt/apache-zookeeper-3.8.0-bin/data 
```

### 同步到其它节点

```bash
scp -r /opt/apache-zookeeper-3.8.0-bin slave1:/opt/
scp -r /opt/apache-zookeeper-3.8.0-bin slave2:/opt/
scp /etc/profile slave1:/etc/
scp /etc/profile slave2:/etc/
source /etc/profile #三个节点都要
```

### 在data目录下创建myid文件

```bash
nano /opt/apache-zookeeper-3.8.0-bin/data/myid     #三个节点
```

三个节点master,slave1,slave2分别写入0，1，2

这个数字用来唯一标识这个服务，一定要保证唯一性，zookeeper会根据这个id来取出server.x上的配置

### 同步时间

```bash
apt-get install ntp
```

`ntpdate -u s2c.time.edu.cn`    #不确定

经常挂起虚拟机，可能会导致时间停止，可使用crontab做成定时同步      #不确定

```bash
crontab -e
```

 进入crontab命令编辑模式

加入以下内容(每十分钟同步一次)：

```bash
*/10 * * * * ntpdate -u s2c.time.edu.cn
```

### 启动zk

```bash
zkServer.sh start #三台都需要执行
zkServer.sh status #查看状态，必须全部启动!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!
```

如果后续出现问题，可以删除data目录下的version文件, 所有节点都要删除

`rm -rf /opt/apache-zookeeper-3.8.0-bin/data/version-2`

## **HBase安装**

### 通过镜像下载

[https://mirrors.tuna.tsinghua.edu.cn/apache/hbase/3.0.0-alpha-3/hbase-3.0.0-alpha-3-bin.tar.gz](https://mirrors.tuna.tsinghua.edu.cn/apache/hbase/3.0.0-alpha-3/hbase-3.0.0-alpha-3-bin.tar.gz)

解压，**修改hbase-env.sh文件**

```bash
tar -zxvf hbase-3.0.0-alpha-3-bin.tar.gz -C /opt/  #解压
cd /opt/hbase-3.0.0-alpha-3/conf  #进入到配置文件目录
nano hbase-env.sh
export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64  #增加java配置
export HBASE_MANAGES_ZK=false   #关闭默认zk配置
```

### 修改hbase-site.xml文件

`nano hbase-site.xml`

```xml
<!--hbase.root.dir 将数据写入哪个目录-->
<property>
<name>hbase.rootdir</name>
<value>hdfs://master:9000/hbase</value>
</property>
<!--单机模式不需要配置，分布式配置此项,value值为true,多节点分布-->
<property>
<name>hbase.cluster.distributed</name>
<value>true</value>
</property>
<property>
<!--单机模式不需要配置多个IP，分布式配置此项value值为多个主机ip,多节点分布-->
<name>hbase.zookeeper.quorum</name>
<value>slave1,slave2,master</value>
</property>
<property>
<name>dfs.replication</name>
<value>3</value>
 </property>
<property>
<name>hbase.master.info.port</name>
<value>16010</value>
</property>
<property>
<name>hbase.master.info.bindAddress</name>
<value>0.0.0.0</value>
</property>
```

### 修改regionservers文件
分布式的在这里列出了你希望运行的全部 HRegionServer，一行写一个host (就像Hadoop里面的 slaves 一样
改为自己的子节点主机名

`nano regionservers`

```
slave1
slave2
```

**同步到所有节点**

```bash
scp -r /opt/hbase-3.0.0-alpha-3 slave1:/opt/
scp -r /opt/hbase-3.0.0-alpha-3 slave2:/opt/
```

### 配置环境变量

`nano /etc/profile`

```bash
export HBASE_HOME=/opt/hbase-3.0.0-alpha-3
export PATH=$PATH:$HBASE_HOME/bin
```

同步配置环境变量

```bash
scp /etc/profile slave1:/etc/
scp /etc/profile slave2:/etc/
source /etc/profile   #在所有节点执行使新配置的环境变量生效
```

### **启动hbase集群 ， 需要在master上执行**

`start-hbase.sh  #开启，必须开启ssh免密登录!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!`

`stop-hbase.sh  #关闭`

通过jps查看下进程需要有

`hmaster`

`hregionServer`

**验证hbase**
管理界面
http://localhost:16010

通过 `hbase shell`

进入到hbase的命令行

创建表 列簇 列式数据库

```sql
create 'test','info'
```

插入数据

```sql
put 'test','001','info:name','zhangsan’
```

查询数据

```sql
get 'test','001’
```

**如果出现问题，重置hbase**

删除hdfs数据

```bash
hadoop dfs -rmr /hbase
```

删除元数据 zk

```bash
zkCli.sh
```

```bash
rmr /hbase
```
