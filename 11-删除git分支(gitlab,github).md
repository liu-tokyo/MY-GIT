## 删除git（gitlab，github）的分支



### １．通过代码删除

- 命令：**$ git push origin 【空格】【冒号】【需要删除的分支名字】**

  比如我`github`上有`master`和`feature`分支，我现在想着删除`feature`分支，命令如下：

  ```bash
  $ git push origin :feature
  ```

  OK，这样你`github`上的远程分支就被删除了。别问为什么，就是删除了！！！  
  ![a](https://img-blog.csdn.net/20151022183544131?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

  ※ 后面的办法尚未验证，至少这个奇怪的方法，还真的有效！  
  不过，对于普通用户，还是直接登录 GIT 网站，手动删除更为直观，不需要记住各种复杂的指令。

### ２．通过客户端删除

- 登录 GIT 网站，找到各种分支，点击右边的删除按钮即可  
  ![b](https://img-blog.csdn.net/201805301035080?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3Rhb3d1aHVhMDUwNQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

### ３． git branch -d <BranchName> 删除本地分支

- 指令如下  
  ![c](https://img-blog.csdnimg.cn/20200723154657103.png)

### ４．git push origin --delete <BranchName> 删除远程分支
