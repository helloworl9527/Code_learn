## HDFS概述

![image-20230821103033850](imgs\image-20230821103033850.png)

​        Hadoop Distributed File System，简称 HDFS，是一个分布式文件系统。HDFS 有着高容错性（fault-tolerent）的特点，并且设计用来部署在低廉的（low-cost）硬件上。而且它提供高吞吐量（high throughput）来访问应用程序的数据，适合那些有着超大数据集（large data set）的应用程序。

> **注意：**
>
> 分布式文件系统能够横跨N个机器





### HDFS的设计目标

- Hardware failure 
  		每个机器只存储文件的部分数据，blocksize=128M
    		block存放在不同的机器上，由于容错，HDFS默认采用3副本机制
- Streaming Data Access  流式数据访问
  重点在于数据访问的高吞吐量，而不是数据访问的低延迟
- Large Data Sets  大规模数据集
  	Moving Computation is Cheaper than Moving Data	移动计算比移动数据更划算



### HDFS的架构 (Master-Slave，主从架构)

![1713999350699](imgs\1713999350699.png)



#### NameNode

- NameNode是整个文件系统的管理节点（包含NameNode的服务器为Master节点），它主要维护着整个文件系统的文件目录树、文件/目录的元信息，每个文件对应的数据块列表，并且还负责接收用户的请求操作 
- NameNode主要包含以下文件：fsimage、edits、seen_txid、VERSION。这些文件的路径由core-site.xml文件中的hadoop.tmp.dir属性控制。

总结：NameNode维护了两份关系：

第一份关系：File与Block list的关系，对应的关系信息存储在fsimage和edits文件中（当NameNode启动的时候会把文件中的内容加载到内存中）

第二份关系：DataNode与Block的关系（当DataNode启动的时候，会把当前节点上的Block信息和节点信息上报给NameNode）



#### DataNode

- 提供真实文件数据的存储服务，包含DataNode的服务器为Slave节点。
- HDFS会按照固定的大小和顺序对文件进行划分并编号，划分好的每一个块称为一个Block，HDFS默认每个Block大小是128M。
- HDFS中，如果一个文件小于一个数据块的大小，那么并不会占用整个数据块的存储空间



#### SecondaryNameNode

​     辅助 NameNode 管理元数据信息，定期地把edits文件中的内容合并到fsimage中，这个合并操作称为checkpoint。

注意：在NameNode的HA（High Availabel，高可用）架构中是没有SecondaryNameNode进程的，这时候原来SecondaryNameNode的工作交由standby NameNode负责实现。



##### 补充了解：

**Erasure Coding**：Hadoop 3引入了纠删码（Erasure Coding）作为数据冗余和容错的替代方法。与传统的三副本复制相比，Erasure Coding更加节省存储空间，减少了数据的冗余复制。

例如：如果按照Hadoop3之前的传统解决方案，一个具有 6 个 blocks 的文件，复制因子为 3，将占用磁盘空间的 18 个 blocks。

![1713857171385](imgs\1713857171385.png)

​	  而Hadoop3引入了 Erasure Coding（纠删码），那么一个具有 6 个 blocks 的文件将仅占用磁盘空间的 9 个 blocks（6 data，3 parity）。

![1713857243787](imgs\1713857243787.png)



## HDFS命令行操作  

![image-20230822155314677](imgs\image-20230822155314677.png)



### Hadoop命令格式

​        如果没有配置 hadoop 的环境变量，则在 hadoop 的安装目录下的 bin 目录中执行

- hdfs dfs 命令
- hadoop fs 命令

这两个命令等效



### hdfs常用选项：

```
-ls 查询指定路径信息
-put  将本地文件上传到hdfs目标地址
-cat 查看HDFS文件内容
-text  查看HDFS文件内容
-get       将hdfs文件下载到本地
-mkdir [-p]  创建目录
-rm [-r] 删除文件/目录
-cp  拷贝
-mv  剪切
```



#### 操作演示：

```
hdfs dfs -ls /    # 查看HDFS根目录下的内容
hadoop fs -ls /   # 查看HDFS根目录下的内容

# 将本地文件上传到HDFS根目录下
hdfs dfs -put anaconda-ks.cfg /  
hadoop fs -put a.txt /  

# 删除HDFS根目录下的文件
hdfs dfs -rm /a.txt /anaconda-ks.cfg  # 删除HDFS上的两个文件
hadoop fs -rm /a.txt  


# 查看HDFS根目录中a.txt文件的内容
hdfs dfs -cat /a.txt
hdfs dfs -text /a.txt
hadoop fs -text /a.txt
hadoop fs -cat /a.txt

# 下载HDFS根目录下的fruits.txt到本地
hdfs dfs -get /fruits.txt
hadoop fs -get /fruits.txt

# 在HDFS上创建级联目录
hdfs dfs -mkdir -p /country/china
hadoop fs -mkdir -p /country/china

# 在HDFS上删除目录
hdfs dfs -rm -r /country
hadoop fs -rm -r /country

# 将HDFS根目录下的fruits.txt文件拷贝
hdfs dfs -cp /fruits.txt /fruits.txt.bak
hadoop fs -cp /fruits.txt /fruits.txt.bak2

# 将HDFS根目录下的文件修改名称（用到了mv剪切选项命令）
hdfs dfs -mv /fruits.txt /fruits.txt2
hadoop fs -mv /fruits.txt2 /fruits.txt3
```

​	

