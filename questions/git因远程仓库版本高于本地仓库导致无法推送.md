错误如图

![image](https://github.com/helloworl9527/code_learn/blob/main/pictures/questions_1.PNG)

# 此时有两种解决方案
## 1.将远程仓库最新版本拉取到本地仓库合并
将远程仓库分支拉取到本地  
`git pull origin <分支名>`  
如果拉取过程中没有冲突，Git会自动完成合并，直接推送到远程仓库即可  
‘git push origin <分支名>`  
## 2.强制推送
本地仓库直接覆盖远程仓库内容（如果是和别人协作的仓库慎用  
`git push -f origin <分支名>`
