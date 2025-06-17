# 一、安装虚拟机软件及下载对应iso文件
<p><Strong>M系列芯片下载arrch64架构的镜像</Strong></p>

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

---
>[Mac VMware Fusion安装CentOS 7](https://blog.csdn.net/vbirdbest/article/details/107375067)  
>[Mac M1 Vmware Fuison 安装 CentOS Bilbil](https://www.bilibili.com/video/BV1XW4y1y7zv?vd_source=afbdaebbd5c69c97785bec729004fceb) 

