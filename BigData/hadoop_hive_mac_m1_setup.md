# Mac M1 + VMware Fusion 安装配置 Hadoop + Hive + MySQL 开发环境指南

## 一、系统环境

- **宿主机**：MacBook Air (M1 芯片)
- **虚拟机**：CentOS 9 (arrch64架构)，运行于 VMware Fusion
- **Hadoop**：3.2.0  
- **Hive**：4.0.1  
- **MySQL**：8.0  
- **Java**：JDK 8u362  
- **Spark**：3.5.6  

---

## 二、目录结构约定

| 用途               | 路径示例 |
|--------------------|-----------|
| 软件压缩包存放目录 | `/usr/export` |
| 解压后的软件目录   | `/usr/local/` |
| Hadoop 路径        | `/usr/local/hadoop3.2/hadoop-3.2.0` |
| Hadoop 配置目录    | `/usr/local/hadoop3.2/hadoop-3.2.0/etc/hadoop` |
| Hadoop 日志目录    | `/usr/local/hadoop3.2/hadoop_log/data/hadoop_repo/logs/hadoop` |
| Java 安装路径      | `/usr/local/java/jdk8u362-b09` |
| Hive 安装路径      | `/usr/local/Hive/apache-hive-4.0.1-bin` |
| Spark 安装路径     | `/usr/local/Spark/spark-3.5.6-bin-hadoop3` |
| IDEA 启动路径      | `/home/cyh/下载/idea-IU-251.26094.121/bin` 启动：`./idea.sh` |

---

## 三、虚拟机安装 CentOS 9

- **下载地址**：  
  - [CentOS 官网](https://www.centos.org/download/)  
  - [阿里云镜像（建议使用）](https://mirrors.aliyun.com/centos-stream/9-stream/BaseOS/aarch64/iso/)  
  - [VMware Fusion 安装教程](https://sysin.org/blog/vmware-fusion-13/)

> 💡 M系列芯片需下载 arrch64 架构的 ISO 镜像

---

## 四、配置 Hadoop 伪分布式集群

### 1. SSH 免密登录配置

```bash
ssh-keygen -t rsa -P ""
cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys
chmod 600 ~/.ssh/authorized_keys
chmod 700 ~/.ssh
```

### 2. 遇到问题解决方案

- **连接失败**：
  - 使用 `ip addr` 查实际 IP，检查是否是主机 IP。
- **Permission denied**：
  - 修改 `/etc/ssh/sshd_config` 添加：

    ```conf
    PermitRootLogin yes
    PubkeyAuthentication yes
    PasswordAuthentication yes
    ```

  - 重启服务：`systemctl restart sshd`
- **脚本权限不足**：
  ```bash
  chmod +x /usr/local/hadoop3.2/hadoop-3.2.0/sbin/start-dfs.sh
  ```

---

## 五、Hadoop 启动

```bash
cd /usr/local/hadoop3.2/hadoop-3.2.0
./sbin/start-all.sh
```

---

## 六、安装配置 MySQL

### 1. MySQL 初始配置

```bash
systemctl start mysqld
systemctl status mysqld
mysql -u root -p
```

### 2. 重置 root 密码

```bash
sudo systemctl stop mysqld
sudo -u mysql mysqld --skip-grant-tables --skip-networking &
mysql -u root
FLUSH PRIVILEGES;
ALTER USER 'root'@'localhost' IDENTIFIED BY 'MyNewPass123!';
```

---

## 七、安装配置 Hive

### 1. 配置 Hive 使用 MySQL 作为元数据库

#### 修改 `hive-site.xml`：

```xml
<property>
  <name>javax.jdo.option.ConnectionURL</name>
  <value>jdbc:mysql://localhost:3306/hive_meta?createDatabaseIfNotExist=true&amp;serverTimezone=UTC&amp;useSSL=false</value>
</property>

<property>
  <name>javax.jdo.option.ConnectionDriverName</name>
  <value>com.mysql.cj.jdbc.Driver</value>
</property>

<property>
  <name>javax.jdo.option.ConnectionUserName</name>
  <value>hiveuser</value>
</property>

<property>
  <name>javax.jdo.option.ConnectionPassword</name>
  <value>Hive123@!</value>
</property>
```

### 2. 初始化元数据库

```bash
schematool -dbType mysql -initSchema
```

### 3. 启动 Hive 服务

```bash
nohup hive --service metastore > metastore.log 2>&1 &
nohup hive --service hiveserver2 > hiveserver2.log 2>&1 &
```

### 4. 验证端口监听

```bash
netstat -tnlp | grep 9083   # Metastore
netstat -tnlp | grep 10000  # HiveServer2
```

### 5. 连接 Hive

```bash
beeline -u jdbc:hive2://192.168.118.128:10000 -n hiveuser
```

---

## 八、安装 Spark

### 启动 Spark 服务

```bash
cd /usr/local/Spark/spark-3.5.6-bin-hadoop3
./sbin/start-all.sh
```

### 启动 Spark Shell

```bash
spark-shell --master local                # 单机模式
spark-shell --master spark://IP:7077      # Standalone 模式
spark-shell --master yarn                 # YARN 模式（需 Hadoop 已启动）
```

### 启动 spark-sql

```bash
cd $SPARK_HOME
./bin/spark-sql
```

---

## 九、启动 IntelliJ IDEA 并配置 Hadoop 项目

```bash
cd /home/cyh/下载/idea-IU-251.26094.121/bin
./idea.sh
```

> 💡 建议使用 IDEA Ultimate 版本，便于插件管理和大数据开发支持。

---

## 十、常用命令参考

```bash
# 删除文件夹
rm -r 目录名             # 逐个确认删除
rm -rf 目录名            # 强制删除
rm -f 文件名             # 删除单个文件

# 创建目录
mkdir 目录名

# 查看当前路径
pwd
```

---

## 参考链接

- [Mac VMware 安装 CentOS 7](https://blog.csdn.net/vbirdbest/article/details/107375067)
- [M1 VMware Fusion 安装 CentOS 视频教程](https://www.bilibili.com/video/BV1XW4y1y7zv)
- [在 IntelliJ IDEA 中使用 Hadoop 教程](https://blog.csdn.net/shenpkun/article/details/130295946)
- [Idea安装过程图解](https://blog.csdn.net/hang_sugar/article/details/127331445)
