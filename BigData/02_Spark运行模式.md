## Spark运行模式

![1715073508582](imgs\1715073508582.png)

   Spark主要有三种运行模式：

- 本地（单机）模式

​    本地模式通过多线程模拟分布式计算，通常用于对应用程序的简单测试。本地模式在提交应用程序后，将会在本地生成一个名为SparkSubmit的进程，该进程既负责程序的提交，又负责任务的分配、执行和监控等。

- Spark Standalone模式

​     使用Spark自带的资源调度系统，资源调度是Spark自己实现的。

- Spark On YARN模式

​     以YARN作为底层资源调度系统以分布式的方式在集群中运行。





## Spark Standalone架构

![1715073719021](imgs\1715073719021.png)

#### Spark Standalone的两种提交方式

​        Spark Standalone模式为经典的Master/Slave架构，资源调度是Spark自己实现的。在Standalone模式中，根据应用程序提交的方式不同，Driver（主控进程）在集群中的位置也有所不同。应用程序的提交方式主要有两种：client和cluster，默认是client。可以在向Spark集群提交应用程序时使用--deploy-mode参数指定提交方式。

- client提交方式

当提交方式为client时，运行架构如下图所示：

![1715065243094](imgs\1715065243094.png)

​                            **Spark Standalone模式架构（client提交方式）**

​     集群的主节点称为Master节点，在集群启动时会在主节点启动一个名为Master的守护进程；从节点称为Worker节点，在集群启动时会在各个从节点上启动一个名为Worker的守护进程。
​    Spark在执行应用程序的过程中会启动Driver和Executor两种JVM进程。

​    Driver为主控进程，负责执行应用程序的main()方法，创建SparkContext对象（负责与Spark集群进行交互），提交Spark作业，并将作业转化为Task（一个作业由多个Task任务组成），然后在各个Executor进程间对Task进行调度和监控。通常用SparkContext代表Driver。如图所示的架构中，<span style="color:red">Spark会在客户端启动一个名为SparkSubmit的进程，Driver程序则运行于该进程。</span>

​    Executor为应用程序运行在Worker节点上的一个进程，由Worker进程启动，负责执行具体的Task，并存储数据在内存或磁盘上。每个应用程序都有各自独立的一个或多个Executor进程。

- cluster提交方式

​    当提交方式为cluster时，运行架构如下图所示:

​    ![1715065803872](imgs\1715065803872.png)

​                    **Spark Standalone模式架构（cluster提交方式）**

​        Standalone以cluster提交方式提交应用程序后，客户端仍然会产生一个名为SparkSubmit的进程，但是该进程会在应用程序提交给集群之后就立即退出。当应用程序运行时，<span style="color:red">Master会在集群中选择一个Worker启动一个名为DriverWrapper的子进程，该子进程即为Driver进程。</span>



#### Spark Standalone模式的搭建

​      进入Spark安装根目录，进入conf目录，执行以下操作：

(1)：复制spark-env.sh.template文件为spark-env.sh文件

```
cp spark-env.sh.template spark-env.sh
```

(2):  修改spark-env.sh文件，添加以下内容：

```
export JAVA_HOME=/usr/local/java/jdk8u362-b09
export SPARK_MASTER_HOST=my2308-host
export SPARK_MASTER_PORT=7077
```

- JAVA_HOME：指定JAVA_HOME的路径。若节点在/etc/profile文件中配置了JAVA_HOME，则该选项可以省略，Spark启动时会自动读取。为了防止出错，建议此处将该选项配置上。

- SPARK_MASTER_HOST：指定集群主节点（Master）的主机名。
- SPARK_MASTER_PORT：指定Master节点的访问端口，默认为7077。

(3): 启动Spark集群

进入Spark安装目录，执行以下命令，启动Spark集群：

```
sbin/start-all.sh
```

启动完毕后，分别在各节点执行jps命令，查看启动的Java进程。若存在Master进程和Worker进程，则说明集群启动成功。

也可以在浏览器中访问网址Mei，查看Spark的WebUI，查看Spark的WebUI，如图所示：

![1715069822467](imgs\1715069822467.png)





## Spark On YARN架构

![1715073840107](imgs\1715073840107.png)

#### Spark On YARN的两种提交方式

​       Spark On YARN模式遵循YARN的官方规范，YARN只负责资源的管理和调度，运行哪种应用程序由用户自己决定，因此可能在YARN上同时运行MapReduce程序和Spark程序，YARN对每一个程序很好地实现了资源的隔离。这使得Spark与MapReduce可以运行于同一个集群中，共享集群存储资源与计算资源。

​      Spark On YARN模式与Standalone模式一样，也分为client和cluster两种提交方式。

- client提交方式

![1715066986325](imgs\1715066986325.png)

​                   **Spark On YARN模式架构（client提交方式）**

<span style="color:red">客户端会产生一个名为SparkSubmit的进程，Driver程序则运行于该进程中。</span>



- cluster提交方式

![1715067323040](imgs\1715067323040.png)

​          <span style="color:red">ResourceManager会在集群中选择一个NodeManager进程启动一个名为ApplicationMaster的子进程，该子进程即为Driver进程（Driver程序运行在其中）。</span>



#### Spark On YARN模式的搭建

​     Spark On YARN模式的搭建比较简单，仅需要在YARN集群的一个节点上安装Spark即可，该节点可作为提交Spark应用程序到YARN集群的客户端。Spark本身的Master节点和Worker节点不需要启动。

​     使用此模式需要修改Spark配置文件$SPARK_HOME/conf/spark-env.sh，添加Hadoop相关属性，指定Hadoop与配置文件所在目录，内容如下：

```
export HADOOP_HOME=//usr/local/hadoop3.2/hadoop-3.2.0
export HADOOP_CONF_DIR=$HADOOP_HOME/etc/hadoop
```

​      

## Spark应用程序的提交

![1715074112961](imgs\1715074112961.png)

​        Spark提供了一个客户端应用程序提交工具spark-submit，使用该工具可以将编写好的Spark应用程序提交到Spark集群。

**spark-submit的使用格式：**

```
bin/spark-submit [options] <app jar> [app options]
```

options：表示传递给spark-submit的控制参数；

app jar：表示提交的程序jar包（或Python脚本文件）所在位置；

app options：表示jar程序需要传递的参数，例如main()方法中需要传递的参数。

附表：

- spark-submit的常用参数介绍

![1715072468629](imgs\1715072468629.png)

- spark-submit的--master参数取值介绍

![1715072538177](imgs\1715072538177.png)



**举个栗子：以Spark自带的求圆周率的程序提交为例：**

- 在Standalone模式下，将Spark自带的求圆周率的程序提交到Spark自带的资源管理器：

```
bin/spark-submit \
--class org.apache.spark.examples.SparkPi \
--master spark://192.168.121.131:7077 \
/export/servers/spark/examples/jars/spark-examples_2.12-3.3.3.jar
```

<span style="color:red">**注：上面命令中的\符号代表换行！**</span>

可通过：http://虚拟机IP:8080 查看提交的应用程序

- 在Sparn On YARN模式下，将Spark自带的求圆周率的程序提交到Hadoop YARN进行资源管理（注意提前将Hadoop HDFS和YARN启动），并且以Spark On YARN的cluster模式运行，命令如下：

```
bin/spark-submit \
--class org.apache.spark.examples.SparkPi \
--master yarn \
--deploy-mode cluster \
/export/servers/spark/examples/jars/spark-examples_2.12-3.3.3.jar
```

可通过：http://虚拟机IP:8088/ 查看提交的应用程序





## Spark Shell的使用

![1715147611300](imgs\1715147611300.png)

​       Spark带有交互式的Shell，可在Spark Shell中直接编写Spark任务，然后提交到集群与分布式数据进行交互，并且可以立即查看输出结果。Spark Shell提供了一种学习Spark API的简单方式，可以使用Scala或Python语言进行程序的编写。

​      执行以下命令，可以查看Spark Shell的相关使用参数：

```
spark-shell --help
```

​      Spark Shell在Spark Standalone模式和Spark On YARN模式下都可以执行，与使用spark-submit进行任务提交时可以指定的参数及取值相同。唯一不同的是，Spark Shell本身为集群的client提交方式运行，不支持cluster提交方式，即使用Spark Shell时，Driver运行于本地客户端，而不能运行于集群中。

- 本地（单机）模式启动Spark Shell终端

```
spark-shell --master local
```

- Spark Standalone模式启动Spark Shell终端

```
spark-shell --master spark://虚拟机IP:7077
```

![1715148246823](imgs\1715148246823.png)

​    从启动过程的输出信息可以看出，Spark Shell启动时创建了一个名为sc的变量，该变量为类SparkContext的实例，可以在Spark Shell中直接使用。SparkContext存储Spark上下文环境，是提交Spark应用程序的入口，负责与Spark集群进行交互。除了创建sc变量外，还创建了一个spark变量，该变量是类SparkSession的实例，也可以在Spark Shell中直接使用。

​     启动完成后，访问Spark WebUI http://虚拟机IP:8080查看运行的Spark应用程序，如图：

![1715148643091](imgs\1715148643091.png)

可以看到，Spark启动了一个名为Spark shell的应用程序（如果Spark Shell不退出，该应用程序就一直存在）。这说明，实际上Spark Shell底层调用了spark-submit进行应用程序的提交。与spark-submit不同的是，Spark Shell在运行时会先进行一些初始参数的设置，并且Spark Shell是交互式的。

- Spark On YARN模式启动Spark Shell终端（别忘了启动Hadoop）

```
spark-shell --master yarn
```

​    注意：若启动过程中报错如图所示，则说明Spark任务的内存分配过小，YARN直接将相关进程杀掉了。

![1715148866546](imgs\1715148866546.png)

此时只需要在Hadoop的配置文件yarn-site.xml中加入以下内容即可：

```
    <!--关闭物理内存检查-->
    <property>
        <name>yarn.nodemanager.pmem-check-enabled</name>
        <value>false</value>
    </property>
    <!--关闭虚拟内存检查-->
    <property>
        <name>yarn.nodemanager.vmem-check-enabled</name>
        <value>false</value>
    </property>
```

然后重启YARN即可。