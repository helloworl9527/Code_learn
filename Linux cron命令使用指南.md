unbuntu20.4  
系统自带cron  
# 快速使用
1.crontab -e #编辑定是任务列表  ，第一次运行时需要选择文本编辑器根据自己熟悉的选择即可（nano/vim/ed)  
2.命令格式  
[Linux定时任务-Cron表达式详解](https://blog.csdn.net/longgeaisisi/article/details/90400969)  
示例  
0 2 * * * touch test.text #每天凌晨2点执行touch test.text命令(创建test.text文本文件）  
## 注意
如果使用境外服务器需要注意服务器时间是否与你当地时间保持一致

# 命令列表
```
安装：apt-get install cron
启动：service cron start
重启：service cron restart
停止：service cron stop
检查状态：service cron status
查询cron可用的命令：service cron
检查Cronta工具是否安装：crontab -l

-u user：用来设定某个用户的crontab服务；
file：file是命令文件的名字,表示将file做为crontab的任务列表文件并载入crontab。如果在命令行中没有指定这个文件，crontab命令将接受标准输入（键盘）上键入的命令，并将它们载入crontab。
-e：编辑某个用户的crontab文件内容。如果不指定用户，则表示编辑当前用户的crontab文件。
-l：显示某个用户的crontab文件内容，如果不指定用户，则表示显示当前用户的crontab文件内容。
-r：从/var/spool/cron目录中删除某个用户的crontab文件，如果不指定用户，则默认删除当前用户的crontab文件。
-i：在删除用户的crontab文件时给确认提示。

```

