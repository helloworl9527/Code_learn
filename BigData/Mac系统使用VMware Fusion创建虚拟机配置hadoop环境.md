Hadoop:3.2<br>
Hive:4.0<br>
mysql:8.0<br>


# 一、安装虚拟机软件及下载对应iso文件
<p><Strong>M系列芯片下载arrch64架构的镜像</Strong></p>
<p>/usr/export存放软件压缩包</p>
<p>/usr/local/存放软件</p>
<p>hadoop目录:/usr/local/hadoop3.2/hadoop-3.2.0   启动：./sbin/start-all.sh</p>a
<p>java:/usr/local/java/jdk8u362-b09</p>
<p>hadoop日志:/usr/local/hadoop3.2/hadoop_log/data/hadoop_repo/logs/hadoop</p>
<p>hadoop配置文件目录:/usr/local/hadoop3.2/hadoop-3.2.0/etc/hadoop</p>
<p>Idea启动目录:/home/cyh/下载/idea-IC-251.26094.121/bin  启动./idea.sh</p>
<p>mysql密码：MyPass123! 启动mysql:mysql -u root -p</p>
<p>Hive安装目录：/usr/local/Hive/apache-hive-4.0.1-bin</p>
<p>
启动idea:cd /home/cyh/下载/idea-IU-251.26094.121/bin
<br>
./idea.sh
</p>

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
爱我的
### 下载Idea

### 


---
>[Mac VMware Fusion安装CentOS 7](https://blog.csdn.net/vbirdbest/article/details/107375067)  
>[Mac M1 Vmware Fuison 安装 CentOS Bilbil](https://www.bilibili.com/video/BV1XW4y1y7zv?vd_source=afbdaebbd5c69c97785bec729004fceb) 

