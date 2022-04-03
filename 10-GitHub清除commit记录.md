## GitHub清除commit记录

---

### 目录

- [GitHub清除commit记录](#github清除commit记录)
  - [目录](#目录)
  - [１．问题描述：](#１问题描述)
  - [２．解决方案：](#２解决方案)
    - [2.1 删除本地原来的仓库](#21-删除本地原来的仓库)
    - [2.2 把所有文件重新加入仓库](#22-把所有文件重新加入仓库)

---

### １．问题描述：
在搭建博客等项目场景下，常常会出现多次commit使得仓库变大（记录了历史版本），GitHub上的commit次数过多，希望清除历史版本保留最新版本的文件，可以考虑用下面的方式重置仓库。



### ２．解决方案：
#### 2.1 删除本地原来的仓库
来到本地仓库路径下
windows系统下，直接右键删除，Linux系统可使用如下命令：

```bash
# Linux
sudo rm .git -r

# Windows(居然依然是Linux指令，无非是不需要sudo而已)
rm -rf .git
```

初始化一个新的仓库

```bash
git init
```

username 替换为你的用户名，**repo_name**替换为原来的仓库名

```bash
git remote add origin https://github.com/username/repo_name.git
```


查看是否配置成功 (返回fetch、push两条信息，连接和上面配置的相同）

```bash
git remote -v
```

#### 2.2 把所有文件重新加入仓库
  ```bash
  git add --all
  ```


   commit注解“reset my repository”可自定义

```bash
git commit -am 'reset my repository'
```


将文件push到远程仓库

```bash
git push -f origin master
```

