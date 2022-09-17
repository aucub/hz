---
title: hadoop
tags: 
  - hadoop
keywords: hadoop,docker,wsl
abbrlink: b4989874
date: 2022-09-16 14:25:21
updated: 2022-09-16 14:25:21
---

抄自：

[https://www.brain-ol.com/posts/docker-hadoop.html](https://www.brain-ol.com/posts/docker-hadoop.html)

<!-- more -->

## docker
### Docker 换源

编辑 /etc/docker/daemon.json 文件，将配置放入其中

```jsx
{
"registry-mirrors": [
"[http://registry.docker-cn.com](http://registry.docker-cn.com/)",
"[http://docker.mirrors.ustc.edu.cn](http://docker.mirrors.ustc.edu.cn/)",
"[http://hub-mirror.c.163.com](http://hub-mirror.c.163.com/)"
],
"insecure-registries": [
"[registry.docker-cn.com](http://registry.docker-cn.com/)",
"[docker.mirrors.ustc.edu.cn](http://docker.mirrors.ustc.edu.cn/)"
],
"debug": true,
"experimental": true
}
```

### wsl**启动**Docker

genie 有三个指令：

genie -i 启动 systemd 进程  
genie -s 启动 systemd 进程，并进入该环境终端    
genie -c <command> 启动 systemd 进程，并执行相应的指令

启动docker服务

`genie -c systemctl start docker`

测试：可以使用命令 sudo docker run hello-world 运行你的第一个容器

### **Portainer：Docker管理器**

我们可以使用 `sudo docker search portainer-ce` 来搜索该镜像

`sudo docker pull portainer/portainer-ce`

根据 protainer 官方的例子，运行以下命令启动 portainer

```bash
 sudo docker volume create portainer_data
 sudo docker run -d -p 30000:9000 --name=portainer --restart=always -v /var/run/docker.sock:/var/run/docker.sock -v portainer_data:/data portainer/portainer-ce
```

使用浏览器打开网址 [http://localhost:30000](http://localhost:30000/) 打开 Portainer 网页，设置管理员账号和密码

选择 Local 模式，Connect 连接

### 接下来我们就需要使用 Portainer 搭建 Hadoop 集群，首先我们需要准备一个 ubuntu（使用hadoop官方镜像：`docker pull apache/hadoop:3`），作为 master 节点，首先点击左侧菜单，选择 Images，输入 ubuntu:latest，选择 Pull the Image

### **设置网络**

为了让 3 个节点的 linux 都在一个局域网内，以便之后更方便的搭建集群，我们先创建一个网络，点击左侧的 Networks，点击 `Add network`，添加新网络

随意输入一个网络名:`HadoopCluster`，将 `Enable manual container attachment` 设置为 True，其他选择默认的 bridge 网络即可

创建完毕后，可以看到这个 `HadoopCluster` 的网段是 `172.18.0.0`

### **创建容器**

我们首先来创建集群中的第一个节点，主节点 (master)

选择左侧菜单 `Container`, 点击 `Add container` 按钮

命名这个容器为 master，使用 `ubuntu:lastest` 镜像

`Manual network port publishing`手动添加端口映射hadoop:9870和yarn:8088以及hbase:16010

然后往下翻，在 `Command & logging` 中，设置 Command 为 `tail -f /dev/null`，保证容器挂起，而不是每次启动后自动关机，控制端使用 `Interactive & TTY` 模式

在 `Network` 中，选择 Network 为我们刚才创建的网络 `HadoopCluster`，输入 hostname 为 `master`，~~因为 docker 容器每次启动都会重新生成 hosts，所以我们在创建的时候，就需要直接编辑好 Hosts file entries，否则每次启动后都要手动编辑 hosts 文件。~~

在 `Runtime & Resources` 中，保证容器内的容器可以拿到真实权限，并可以进行资源占用设置，可以 unlimited 无限制，或者限制内存

在 `Capabilities` 中，设置 `SYS_TEM` /`SYS_ADMIN`为 True，保证容器能够拥有足够的权限

最后，点击 `Deploy the container` 按钮，部署该容器

### **配置容器**

容器创建好后，我们就可以在 Containers，选择刚创建的 master 容器，点击 `Console` 按钮，连接到 Ubuntu 进行配置

运行命令 `apt update && apt install openssh-server iputils-ping net-tools nano` 更新并安装必要工具

换源

/etc/apt/sources.list

```bash
deb [https://mirrors.tuna.tsinghua.edu.cn/ubuntu/](https://mirrors.tuna.tsinghua.edu.cn/ubuntu/) jammy main restricted universe multiverse
deb [https://mirrors.tuna.tsinghua.edu.cn/ubuntu/](https://mirrors.tuna.tsinghua.edu.cn/ubuntu/) jammy-updates main restricted universe multiverse
deb [https://mirrors.tuna.tsinghua.edu.cn/ubuntu/](https://mirrors.tuna.tsinghua.edu.cn/ubuntu/) jammy-backports main restricted universe multiverse
deb [https://mirrors.tuna.tsinghua.edu.cn/ubuntu/](https://mirrors.tuna.tsinghua.edu.cn/ubuntu/) jammy-security main restricted universe multiverse
```

使用命令 `nano /etc/apt/sources.list` 编辑源，`Ctrl+K` 删除所有内容，然后将以下文本替换其中

使用 `Ctrl+X` 退出，输入 Y 保存，并确定保存路径

使用命令 `apt update && apt dist-upgrade` 更新源，并更新系统所有软件

使用命令 `passwd` 设置 root 用户登录密码

### Jdk

```bash
apt-get install software-properties-common   #注意
apt-get update
apt-get install openjdk-8-jdk
```

### **环境变量**

使用命令 `nano /etc/profile` 编辑系统环境变量文件，将下面的文本添加进环境变量

```bash
#JAVA
export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64
export PATH=${JAVA_HOME}/bin:$PATH
```

运行命令 `source /etc/profile`，更新系统变量

然后运行命令 `java -version`，查看 JDK 是否安装成功

### **准备自己子节点**

按照同样的步骤，我们再次创建 2 台 ubuntu，作为 slave1 节点和 slave2 节点

可以不用端口映射，之后使用 master 节点连接另外 2 台节点即可

### **Host**

使用命令 `nano /etc/hosts`，将以下信息编辑进去，方便互相通信

```bash
172.18.0.2    master
172.18.0.3    slave1
172.18.0.4    slave2
```

### **修改 OpenSSH-server**

因为 root 默认情况下不允许 SSH 登录，所有我要修改一下 ssh-server 的配置

使用命令 `nano /etc/ssh/sshd_config` 编辑 SSH 的设置，修改为

`PermitRootLogin yes`

使用命令 `service ssh restart` 重启 SSH 服务，这样我们就可以可以使用 XShell 工具远程登录 root 账户了

### SSH免密登录  

**生成密钥对**

使用命令 `ssh-keygen -t rsa`，生成密钥对，一直回车

该命令将在～/.ssh 目录下面产生一个密钥 id_rsa 和一个公钥 id_rsa.pub

**免密登录本机**
首先使用下面的命令，将公钥发放给自己

```bash
cp ~/.ssh/id_rsa.pub ~/.ssh/authorized_keys
```

**可以手动建立 authorized_keys 文件，手动将公钥粘贴进去**

如果权限不正确，请使用下面的命令修改权限

`chmod 600 authorized_keys`

然后使用下面的命令，免密连接本机，测试是否成功

第一次登录需要进行一次验证，输入 yes 即可

 `ssh localhost`

然后可通过 exit，退出登录，返回之前的页面

接下来在每一台节点上，重复运行命令 `ssh-keygen -t rsa`，生成密钥对

使用命令 `cat ~/.ssh/id_rsa.pub`，查看公钥

**将内容复制到主机 master 节点上的 `~/.ssh/authorized_keys`文件中**

然后使用下面的命令，将 authorized_keys 文件传给，其他节点的相同位置

```bash
scp authorized_keys root@slave2:/root/.ssh/
```

```bash
scp authorized_keys root@slave1:/root/.ssh/
```

这样子，我们就让每一台机器都持有了集群中所有节点的公钥，任意机器都可以通过 ssh 登录到任意节点

## **Hadoop**

### **下载**

首先[点击这里](https://hadoop.apache.org/releases.html)，进入 Hadoop 官网下载页面。

选择 3.3.4稳定版本进行下载，然后点击 binary 地址进行下载

[镜像下载地址](https://mirrors.tuna.tsinghua.edu.cn/apache/hadoop/common/stable/)

在 ubuntu 中使用 `wget` 命令进行下载

### **解压**

 `tar -xvzf hadoop-3.3.4.tar.gz -C /opt`

使用命令解压，我们统一放到路径 `/opt` 下

此时 hadoop 的文件结构为

```bash
$ls /opt/hadoop-3.3.4
LICENSE.txt
NOTICE.txt
README.txt
bin
etc
include
lib
libexec
sbin
share
```

### **环境变量**

使用命令 `nano /etc/profile` 编辑系统环境变量文件，将下面的文本添加进环境变量

```bash
#HADOOP
export HADOOP_HOME=/opt/hadoop-3.3.4
export PATH=${HADOOP_HOME}/bin:${HADOOP_HOME}/sbin:$PATH
```

运行命令 `source /etc/profile`，更新系统变量

### **配置**

hadoop 的部署分为 3 种模式，分别为**单机模式** **伪分布模式 (单节点)** **全分布模式**三种

无论部署哪种模式，我们都需要先配置环境变量，我们选择配置系统变量，无论是否是当前路径都可以使用

首先打开 `/opt/hadoop-3.3.4/etc/hadoop` 这个目录，分别编辑下面几个文件，根据个人需求更改参数：

 `nano core-site.xml`

```
<configuration>
  # 配置hdfs的namenode的地址，使用的是hdfs协议
  <property>
    <name>fs.defaultFS</name>
    <value>hdfs://master:9000</value>
  </property>
  # 配置hadoop运行时产生数据的存储目录，不是临时目录
  <property>
    <name>hadoop.tmp.dir</name>
    <value>/opt/tmp</value>
  </property>
</configuration>
```

master 在 hosts 文件中做了映射，可以替换成本机 IP

 `nano hdfs-site.xml`

```
<configuration>
  # 配置在hdfs中，一份文件存几份，默认是3份，一台机器只能存一份，小于datanode数量
  <property>
    <name>dfs.replication</name>
    <value>3</value>
  </property>
  # 是否打开权限检查系统
  <property>
    <name>dfs.permissions</name>
    <value>false</value>
  </property>
  # 命名空间和事务在本地文件系统永久存储的路径
  <property>
    <name>dfs.namenode.name.dir</name>
    <value>/opt/data/namenode</value>
  </property>
  # DataNode在本地文件系统中存放块的路径
  <property>
    <name>dfs.datanode.data.dir</name>
    <value>/opt/data/datanode</value>
  </property>
<property>
<name>dfs.http.address</name>
<value>0.0.0.0:9870</value>
</property>
</configuration>
```

如果你只有 3 个 datanode，但是你却指定副本数为 4，是不会生效的，因为每个 datanode 上只能存放一个副本。

hadoop 有时候并不能自己创建 namenode 和 datanode 文件夹，可以运行下面的命令手动创建这 2 个文件夹

```bash
mkdir -p /opt/data/namenode
mkdir -p /opt/data/datanode
mkdir -p /opt/tmp
```

 `nano yarn-site.xml`

```
<configuration>
  # 指定yarn的resourcemanager的地址（该地址是resourcemanager的主机地址，即主机名或该主机的ip地址）
  <property>
    <name>yarn.resourcemanager.hostname</name>
    <value>master</value>
  </property>
  # 指定mapreduce执行shuffle时获取数据的方式
  <property>
    <name>yarn.nodemanager.aux-services</name>
    <value>mapreduce_shuffle</value>
  </property>
</configuration>
```

 `nano mapred-site.xml`

```
<configuration>
  # 指定mapreduce运行在yarn上
  <property>
    <name>mapreduce.framework.name</name>
    <value>yarn</value>
  </property>
  <property>
    <name>yarn.app.mapreduce.am.env</name>
    <value>HADOOP_MAPRED_HOME=/opt/hadoop-3.2.2</value>
  </property>
  <property>
    <name>mapreduce.map.env</name>
    <value>HADOOP_MAPRED_HOME=/opt/hadoop-3.2.2</value>
  </property>
  <property>
    <name>mapreduce.reduce.env</name>
    <value>HADOOP_MAPRED_HOME=/opt/hadoop-3.2.2</value>
  </property>
</configuration>
```

 `nano hadoop-env.sh`

在任意地方添加 JAVA_HOME

```bash
export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64
```

 `nano workers`

```
master
slave1
slave2
```

配置的是所有的从节点，用 IP 也可以，所有配置文件修改完毕后，进入 hadoop 初始化步骤

### **同步环境到其他节点**

到此一个节点上的配置就已经全部配置完毕了

接下来，我们可以使用下面的命令，将 JDK 和 Hadoop 传输到其他节点

```bash
scp /etc/profile slave1:/etc/
scp /etc/profile slave2:/etc/
scp -r /opt/* slave1:/opt/
scp -r /opt/* slave2:/opt/
scp -r /usr/lib/jvm/java-8-openjdk-amd64/* slave1:/usr/lib/jvm/java-8-openjdk-amd64/   #我不确定
scp -r /usr/lib/jvm/java-8-openjdk-amd64/* slave2:/usr/lib/jvm/java-8-openjdk-amd64/  #不确定
```

### **Hadoop 初始化**

使用下面的命令进入 hadoop 脚本路径

`cd $HADOOP_HOME/sbin/`

或者

`cd  /opt/hadoop-3.3.4/sbin/`

使用 nano 编辑 `start-dfs.sh` 和 `stop-dfs.sh` 顶部添加

```
HDFS_DATANODE_USER=root
HDFS_DATANODE_SECURE_USER=hdfs
HDFS_NAMENODE_USER=root
HDFS_SECONDARYNAMENODE_USER=root
```

使用 nano编辑 `start-yarn.sh`和 `stop-yarn.sh`顶部添加

```
YARN_RESOURCEMANAGER_USER=root
HADOOP_SECURE_DN_USER=yarn
YARN_NODEMANAGER_USER=root
```

### **HDFS 初始化**

集群模式下，需要对每一台机器进行初始化

接下来到各个节点运行下面的命令

```bash
hdfs namenode -format
```

如果初始化失败，需要用下面的命令手动清空 namenode 和 datanode 文件夹，调整配置后，重新初始化

```bash
rm -rf /opt/data/namenode/*
rm -rf /opt/data/datanode/*
```

### **启动 Hadoop**

**Namenode**

```bash
hdfs --daemon start namenode
hdfs --daemon stop namenode
hdfs --daemon start namenode
```

****Datanode****

```bash
hdfs --daemon start datanode
hdfs --daemon stop datanode
hdfs --daemon start datanode
```

你可以使用上面的命令挨个启动 namenode 和 datanode，如果已配置好 workers 和 ssh 免密登录，你可以使用下面的命令调用脚本直接启动所有 hdfs 进程     

`start-dfs.sh`

****ResourceManager****

```bash
yarn --daemon start resourcemanager
yarn --daemon stop resourcemanager
yarn --daemon start resourcemanager
```

****NodeManager****

```bash
yarn --daemon start nodemanager
yarn --daemon stop nodemanager
yarn --daemon start nodemanager
```

你可以使用上面的命令挨个启动 resourcemanager 和 nodemanager，如果已配置好 workers 和 ssh 免密登录，你可以使用下面的命令调用脚本直接启动所有 yarn 进程

`start-yarn.sh`

### **启动成功**

启动完毕后可以使用 `jps` 命令查看启动的 hadoop 进程

可以访问 http://localhost:9870  查看 HDFS 运行情况

可以访问 http://localhost:8088  查看所有 Yarn 任务的运行情况

### **案例测试**

**PI 值计算**

我们可以使用一个简单的例子来测试一下 hadoop 是否能够正常运行

我们从 hadoop 安装文件夹，启动一个终端，使用下面的命令，计算 pi 值

```bash
hadoop jar $HADOOP_HOME/share/hadoop/mapreduce/hadoop-mapreduce-examples-*.jar pi 10 10
```