## Git客户端 - 保存和清除用户名、密码



**简介：**在使用Git的过程中，不想每次都输入用户名和密码去拉取代码，所以就需要保存这些信息，那么既然有保存了，就必须有清除功能。

### １．配置成全局永久保存

1、配置用户名：username

> git config --global user.name “liu”

2、配置邮箱：user@email

> git config --global user.email “liu@vip.com”

3、配置密码

> git config --global credential.helper store

该命令会记住密码，执行一次 git pull 或 git push 等需要输入密码的命令，输入一次密码。

4、查看配置

> git config --list

5、清除密码

> git config --global --unset credential.helper

### ２．临时保存

1、默认记住15分钟

> git config –global credential.helper cache

2、自定义时间

> git config credential.helper ‘cache –timeout=3600’



### ３．备考

以上处理之后，实际上是设置了`$HOME/.gitconfig`文件的内容，如果需要清楚密码，请编辑该文件，删除`helper = store`行。

```bash
[user]
        name = liu
        email = liu@vip.com
[credential]
        helper = store
```

