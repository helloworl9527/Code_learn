## Hadoop概述

![image-20230820124726185](imgs\image-20230820124726185.png)

- Hadoop之父：Doug Cutting（道格卡丁）
- Hadoop名称由来：Hadoop名字不是一个缩写，而是一个生造出来的词。是Hadoop之父Doug Cutting（道格卡丁）儿子毛绒玩具象命名的
- Hadoop是什么

![image-20230820125238524](imgs\image-20230820125238524.png)

​        Hadoop是一个提供了分布式存储和计算的分布式系统基础架构：用户可以在不了解分布式底层细节的情况下进行使用。

### Hadoop核心组件

- Hadoop Common：支持其他Hadoop模块的通用工具
- Hadoop Distributed File System (HDFS)：HDFS实现将文件分布式存储在很多的服务器上
- Hadoop YARN：YARN实现集群资源管理以及作业的调度分布式计算框架x
- Hadoop MapReduce：MapReduce是基于YARN的、可以实现在多机器上分布式并行计算的系统



### Hadoop核心架构发展历史

![1713855743041](imgs\1713855743041.png)

 Hadoop 3

**主要改进**：

- 支持更多的NameNode和更高的可扩展性，允许构建更大的集群。

- 增加了Erasure Coding（纠删码），提高了存储效率，降低了存储成本。

  

**优点**：

- 更高的数据存储效率：通过Erasure Coding，可以节省大量的存储空间。
- 更大的集群规模：支持更多的数据节点和更大的集群。
- 更强的资源管理：引入了更多的调度策略和容器化支持，使得资源管理更加灵活。





### Hadoop生态圈

- 狭义的Hadoop：是一个适合大数据分布式存储（HDFS）、分布式计算

  （MapReduce）和资源调度（YARN）的平台

- 广义的Hadoop：指的是Hadoop生态系统，Hadoop生态系统是一个很庞大的概念，Hadoop是其中最重要最基础的一个部分；生态系统中的每一子系统只解决某一个特定的问题域（甚至可能更窄），不搞统一型的一个全能系统，而是小而精的多个小系统

  ![image-20230820134436264](imgs\image-20230820134436264.png)



### 常用的Hadoop发行版
- Apache Hadoop
  由 Apache 基金会所开发的分布式系统基础架构

  

- CDH：Cloudera’s Distribution，including Apache Hadoop
  是Hadoop众多分支中的一种，由Cloudera维护，基于稳定版本的Apache Hadoop构建

  

- HDP：Hortonworks Data Platform，是一款企业级大数据平台，它基于 Apache  Hadoop和相关技术构建，提供了一系列工具和服务，用于存储、处理和分析大数据。



### Hadoop3单机伪分布集群安装

以下操作均以root用户操作：

- 配置SSH免密钥登录

​       要安装部署Hadoop3，除了安装JDK外，还要进行SSH免密钥登录功能的配置，这是为了方便进行集群主机间的通信，配置SSH免密钥登录的步骤如下：

（1）在需要进行集群统一管理的虚拟机上输入ssh-keygen -t rsa命令生成密钥（根据提示可以不用输入任何内容，连续按4次Enter键确认即可）。

![1713912867089](imgs\1713912867089.png)

（2）生成密钥操作默认会在root目录下生成一个包含有密钥文件的.ssh隐藏目录。执行cd /root/.ssh命令进入.ssh隐藏目录，在该目录下执行ll -a命令查看当前目录下的所有文件。其中，id_rsa和id_rsa.pub这两个文件分别是私钥文件和公钥文件。

（3）为了便于文件配置和虚拟机通信，通常会对主机名和IP做映射配置，在虚拟机执行vi /etc/hosts命令编辑映射文件hosts，在映射文件中添加如下内容：

​      虚拟机IP 主机名

![1713913456511](imgs\1713913456511.png)

<span style="color:red">注意：如果要修改主机名，可使用CentOS7新增加的hostnamectl命令进行修改，语法为：hostnamectl set-hostname 修改后的主机名</span>

 （4）在虚拟机上执行"ssh-copy-id 主机名"命令，将公钥复制到相关联的虚拟机（包括自身）

![1713913861026](imgs\1713913861026.png)

（5）在本虚拟机上执行"ssh 目标机器"命令连接到目标机器上，进行验证免密钥登录操作，此时无需输入密码便可直接登录目标虚拟机进行操作，如需返回本虚拟机，执行exit命令即可。

- 关闭防火墙

  查看防火墙状态：    

```
firewall-cmd --state
```

​     停止防火墙服务：  

```
systemctl stop firewalld
```

​     禁用防火墙服务，确保其在系统重新启动后不会自动启动

```
systemctl disable firewalld
```

- Hadoop 伪分布集群安装

​     下面开始在 1 台 linux 虚拟机上开始安装 Hadoop3 伪分布环境，在这里我们使用 hadoop3.2.0 版本：hadoop-3.2.0.tar.gz

（1）把 hadoop-3.2.0.tar.gz 安装包上传到 linux 机器的/export/software/ 目录下

（2）解压 hadoop 安装包到/export/servers目录下

（3）进入配置文件所在目录 cd hadoop-3.2.0/etc/hadoop/

（4）修改 hadoop-env.sh 文件，增加环境变量信息





export JAVA_HOME=/usr/local/java/jdk8u362-b09

export PATH=$PATH:$JAVA_HOME/bin

export CLASSPATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar



```
export JAVA_HOME=/usr/local/java/jdk8u362-b09
export HADOOP_LOG_DIR=/usr/local/hadoop3.2/hadoop_log/data/hadoop_repo/logs/hadoop
```

![1713914997582](imgs\1713914997582.png)

注意：需要先创建/export/data/ /logs/hadoop日志目录。

 （5）修改 core-site.xml 文件，注意 fs.defaultFS 属性中的主机名需要和你配置的主机名保持一致

```
<configuration>
   <property>
       <name>fs.defaultFS</name>
       <value>hdfs://my2308-host:9000</value>
   </property>
   <property>
       <name>hadoop.tmp.dir</name>
       <value>/export/data/hadoop_repo</value>
   </property>
</configuration>
```

  （6）修改 hdfs-site.xml 文件，把 hdfs 中文件副本的数量设置为 1，因为现在伪分布集群只有一个节点

```
<configuration>
    <property>
       <name>dfs.replication</name>
       <value>1</value>
    </property>
</configuration>
```

 （7）修改 mapred-site.xml，设置 mapreduce 使用的资源调度框架

```
<configuration>
    <property>
       <name>mapreduce.framework.name</name>
       <value>yarn</value>
    </property>
</configuration>
```

 （8）修改 yarn-site.xml，设置 yarn 上支持运行的服务和环境变量白名单

![1713915546395](imgs\1713915546395.png)

```
<configuration>

<!-- Site specific YARN configuration properties -->
   <property>
       <name>yarn.nodemanager.aux-services</name>
       <value>mapreduce_shuffle</value>
   </property>
   <property>
       <name>yarn.nodemanager.env-whitelist</name>
       <value>JAVA_HOME,HADOOP_COMMON_HOME,HADOOP_HDFS_HOME,HADOOP_CONF_DIR,CLASSPATH_PREPEND_DISTCACHE,HADOOP_YARN_HOME,HADOOP_MAPRED_HOME</value>
   </property>
</configuration>
```

 （9）格式化 namenode

```
cd /export/servers/hadoop-3.2.0

bin/hdfs namenode -format
```

如果在后面的日志信息中能看到这一行，则说明 namenode 格式化成功。
common.Storage: Storage directory xxx has been successfully
formatted.

（10）修改start-dfs.sh文件（在hadoop-3.2.0/sbin目录下），在文件前面增加如下内容

```
HDFS_DATANODE_USER=root
HDFS_DATANODE_SECURE_USER=hdfs
HDFS_NAMENODE_USER=root
HDFS_SECONDARYNAMENODE_USER=root
```

（11）修改stop-dfs.sh文件（在hadoop-3.2.0/sbin目录下），在文件前面增加如下内容

```
HDFS_DATANODE_USER=root
HDFS_DATANODE_SECURE_USER=hdfs
HDFS_NAMENODE_USER=root
HDFS_SECONDARYNAMENODE_USER=root
```

（12）修改 start-yarn.sh文件（在hadoop-3.2.0/sbin目录下），在文件前面增加如下内容

```
YARN_RESOURCEMANAGER_USER=root
HADOOP_SECURE_DN_USER=yarn
YARN_NODEMANAGER_USER=root
```

（13）修改stop-yarn.sh 文件（在hadoop-3.2.0/sbin目录下），在文件前面增加如下内容

```
YARN_RESOURCEMANAGER_USER=root
HADOOP_SECURE_DN_USER=yarn
YARN_NODEMANAGER_USER=root
```

（14）启动 hadoop 集群

```
sbin/start-all.sh
```

（15）验证集群进程信息

执行 jps 命令可以查看集群的进程信息，除了jps 这个进程之外还需要有 5 个进程才说明集群是正常启动的

![1713916541592](imgs\1713916541592.png)

还可以通过 webui 界面来验证集群服务是否正常:

hdfs webui 界面：http://虚拟机IP:9870
yarn webui 界面：http://虚拟机IP:8088

（16）停止hadoop集群

```
sbin/stop-all.sh
```

 
