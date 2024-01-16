## GIT 相关知识



- GIT 基本概念
- GIT 安装
- GIT 使用技巧



## Gitlab配置`SSH Key`

> 参照：https://www.cnblogs.com/hafiz/p/8146324.html

- 打开本地`git bash`,使用如下命令生成ssh公钥和私钥对：

  ```
  ssh-keygen -t rsa -C 'xxx@xxx.com'
  ```

  然后一路回车(-C 参数是你的邮箱地址)

- 然后打开`~/.ssh/id_rsa.pub`文件，复制其中的内容。

  `~`表示用户目录，比如我的`windows`就是`C:\Users\Administrator`)

  或者在资源管理器里面输入如下内容，也可以找到公共 KEY 文件。

  ```
  C:\users\%username%\.ssh
  ```

- 打开`gitlab`，找到`Profile Settings`-->`SSH Keys`--->`Add SSH Key`，并把上一步中复制的内容粘贴到`Key`所对应的文本框，在Title对应的文本框中给这个`sshkey`设置一个名字，点击`Add key`按钮

