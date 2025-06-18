Hadoop:3.2<br>
Hive:4.0<br>
mysql:8.0<br>
Mac book air M1芯片的Apple电脑<br>
使用VMware Fusion 虚拟机中安装的Centos9arrch64版本<br>



<p>配置目录信息</p>
<p>/usr/export存放软件压缩包</p>
<p>/usr/local/存放软件</p>
<p>hadoop目录:/usr/local/hadoop3.2/hadoop-3.2.0   启动：./sbin/start-all.sh</p>a
<p>java:/usr/local/java/jdk8u362-b09</p>
<p>hadoop日志:/usr/local/hadoop3.2/hadoop_log/data/hadoop_repo/logs/hadoop</p>
<p>hadoop配置文件目录:/usr/local/hadoop3.2/hadoop-3.2.0/etc/hadoop</p>
<p>Idea启动目录:/home/cyh/下载/idea-IC-251.26094.121/bin  启动./idea.sh</p>
<p>
  Spark安装目录：/usr/local/Spark/spark-3.5.6-bin-hadoop3 <br>
  启动集群:sbin/start-all.sh
  启动shell: <br>
  spark-shell --master local #单机模式 <br>
  spark-shell --master spark://虚拟机IP:7077 #Standalone模式  <br>
  spark-shell --master yarn #YARN模式启动Spark Shell终端（别忘了启动Hadoop）  <br>
</p>
<br>
<p>mysql<br>
密码：MyPass123! <br>
mysql:mysql -u root -p #登陆mysql <br>
systemctl start mysqld #启动mysql <br>
systemctl status mysql #检查mysql状态 <br>
</p>
<br>

<p>
  Hive安装目录：/usr/local/Hive/apache-hive-4.0.1-bin <br>
  启动Hive：
  nohup hive --service metastore \ <br>
  > metastore.log 2>&1 & #后台启动 <br>
  netstat -tnlp | grep 9083 # 应看到: LISTEN 0.0.0.0:9083/java <br>
  nohup hive --service hiveserver2 > hiveserver2.log 2>&1 & #启动Hiveserver2（等待10s左右） <br>
  netstat -tnlp | grep 10000  # 应看到: LISTEN 0.0.0.0:10000/java <br>
  beeline -u jdbc:hive2://127.0.0.1:10000 -n hiveuser -p 'Hive123@!' #使用 Beeline 连接并验证
</p>
<p>
启动idea:cd /home/cyh/下载/idea-IU-251.26094.121/bin  ./idea.sh
</p>

<p>sudo vi /etc/profile #配置环境变量<br>
source /etc/profile #使生效
</p>
<br>

---
# 一、安装虚拟机软件及下载对应iso文件
<p><Strong>M系列芯片下载arrch64架构的镜像</Strong></p>
<br>
<br>
<br>
<p>下载：
<a href='https://www.centos.org/download/'>CentOS</a>
<a href='https://mirrors.aliyun.com/centos-stream/9-stream/BaseOS/x86_64/iso/CentOS-Stream-9-latest-x86_64-dvd1.iso'>阿里云镜像下载</a>
<a href='https://sysin.org/blog/vmware-fusion-13/'>Vmware Fusion</a>
</p>

# 二、配置Hadoop伪分布式集群
<p>配置ssh免密登陆（使用<a href='termius.com'>Termius连接</a>)</p>

## 过程中遇到的错误

### 1.ERROR: Connection closed by 192.168.xxx.xxx port 22
<p>配置的ip地址不是主机ip地址，使用ip addr命令查询本机ip并且后修改</p>

### 2.ERROR: 成功配置ip后输入root密码（确保输入的密码正确）显示 Permission denied, please try again.
<p>vi /etc/ssh/sshd_config #修改配置文件</p>  
添加如下内容<br>
PermitRootLogin yes          # 允许root登录  <br>
PubkeyAuthentication yes     # 启用公钥认证   <br>
PasswordAuthentication yes   # 允许密码认证（临时启用）<br>
<p>systemctl restart sshd #保存后重启ssh</p>

### 3.localhost: root@localhost: Permission denied (publickey,gssapi-keyex,gssapi-with-mic,password).
<p>之前的ssh未正确配置,配置成功输入ssh localhost后无需输入密码直接登陆</p>
ls ~/.ssh/id_rsa.pub #检查是否有ssh密钥<br>ssh-keygen -t rsa -P ""   # 全程回车即可（如果没有）<br>cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys<br>chmod 600 ~/.ssh/authorized_keys #添加公钥到 authorized_keys<br>chmod 700 ~/.ssh #设置 .ssh 目录权限

### 4.sbin/start-all.sh:行57: /usr/local/hadoop3.2/hadoop-3.2.0/sbin/start-dfs.sh: 权限不够
说明start-dfs.sh 脚本没有执行权限，即使是root<br>chmod +x /你的hadoop目录/sbin/start-dfs.sh  #添加执行权限

### 5.HiveServer2不能成功启动
<p>
  是因为Metastore没有成功启动.
  Metastore不能成功启动是没有正确连接mysql
</p>
<p>
  netstat -tnlp | grep 10000 #如果有结果说明 HiveServer2 正常监听 
  nohup hive --service metastore > metastore.log 2>&1 & #先启动 metastore
  netstat -tnlp | grep 9083 #确认 metastore #netstat -tnlp | grep 9083`` 若无输出，说明 metastore 启动失败或未监听端口

  错误原因:Hive Metastore 正在尝试连接 MySQL 数据库，但连接被拒绝
  mysql增加hiveuser用户权限
  nohup hive --service metastore > metastore.log 2>&1 & #重启 metastore

</p>

## 下载hadoop
下载 wget https://archive.apache.org/dist/hadoop/common/hadoop-3.2.0/hadoop-3.2.0.tar.gz.sha256sha256sum -c hadoop-3.2.0.tar.gz.sha2561
<br>
sudo tar -zxvf hadoop-3.2.0.tar.gz -C /usr/local/ #解压到指定目录
<br>
mkdir 文件名 #创建新的文件夹
<br>
rm -r 文件夹名 #删除文件夹（文件夹中每个文件需确认）
<br>
rm -rf 文件夹名 (递归删除不进行确认)
<br>
rm -f 文件名 #删除文件
<br>
pwd #获取当前目录
<br>

## 三、配置idea环境（hadoop)
### 下载Idea
<p>一定要在官网下载Ultimate版本，可免费试用30天，用来做实验够用了。不然后边下载不了插件</p>
<p>然后在Idea中配置Hadoop时候注意要先启动hadoop集群测试连接才能成功</p>
<p>Maven配置启动？</p>
---
## 四、下载配置Hive
<p>安装mysql(手动安装）</p>
<p>mysql JDBC驱动包需单独下载</p>
<p>登陆mysql时使用生成的临时密码无法登陆解决：<br>

```
sudo systemctl stop mysqld
sudo -u mysql mysqld --skip-grant-tables --skip-networking & #使用 mysql 用户身份启动
mysql -u root #无密码登陆
ALTER USER 'root'@'localhost' IDENTIFIED BY '新密码'; #重置密码


```
<p>
ERROR 1290 (HY000): The MySQL server is running with the --skip-grant-tables option so it cannot execute this statement <br>
在 --skip-grant-tables 模式下运行 MySQL，该模式会禁用权限系统，导致无法执行 ALTER USER 这类需要权限验证的操作 <br>
```
FLUSH PRIVILEGES; #刷新权限表
ALTER USER 'root'@'localhost' IDENTIFIED BY 'MyNewPass123!'; #修改密码
```
</p>
</p>

### 启动Hive

```
schematool -dbType mysql -initSchema
Could not create connection to database server. #Hive 在初始化元数据库时无法连接到 MySQL
```
<p>MySQL 8.0+：需使用 mysql-connector-java-8.0.xx.jar</p>
<p>beeline> !connect jdbc:hive2://localhost:10000  #使用该命令前需启动HiveServer2 (HS2)</p>

```
Hive Session ID = 47b1bfb0-0b47-4c0d-8c52-77e46b6b31d6
Hive Session ID = 242ef03b-5dca-4149-b904-de351d236969
Hive Session ID = c15dcafd-3f45-467f-889f-693c31650d98
Hive Session ID = 8a78a138-5228-4b33-a684-7ecb2e0bd2c6
Hive Session ID = b5d38c85-43ff-41ff-bc1d-e06ee278a497
#出现了 SLF4J 日志绑定冲突
```

<p>Hive hive-sit.xml数据库部分配置</p>


```
<!-- 数据库 start -->
    <property>
      <name>javax.jdo.option.ConnectionURL</name>
      <value>jdbc:mysql://localhost:3306/hive_meta?createDatabaseIfNotExist=true&amp;serverTimezone=UTC&amp;useSSL=false</value>
      <description>mysql连接</description>
    </property>

    <property>
      <name>javax.jdo.option.ConnectionDriverName</name>
      <value>com.mysql.cj.jdbc.Driver</value>
      <description>mysql驱动</description>
    </property>

    <property>
      <name>javax.jdo.option.ConnectionUserName</name>
      <value>hiveuser</value>
      <description>数据库使用用户名</description>
    </property>

    <property>
      <name>javax.jdo.option.ConnectionPassword</name>
      <value>Hive123@!</value>
      <description>数据库密码</description>
    </property>
    <!-- 数据库 end -->
```

---
>[Mac VMware Fusion安装CentOS 7](https://blog.csdn.net/vbirdbest/article/details/107375067)  
>[Mac M1 Vmware Fuison 安装 CentOS Bilbil](https://www.bilibili.com/video/BV1XW4y1y7zv?vd_source=afbdaebbd5c69c97785bec729004fceb)  
>[在 IntelliJ IDEA 中使用 Hadoop](https://blog.csdn.net/shenpkun/article/details/130295946?fromshare=blogdetail&sharetype=blogdetail&sharerId=130295946&sharerefer=PC&sharesource=2301_76146126&sharefrom=from_link)  
>[idea安装过程参考](https://blog.csdn.net/hang_sugar/article/details/127331445)

