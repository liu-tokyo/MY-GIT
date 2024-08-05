<font face="simsun">

# Gitlab容器构筑 - 配置邮件服务器

gitlab是代码托管工具，gitlab和github的界面几乎一致。不就是代码托管吗，自己搭！
gitlab有ce和ee两个版本，ce开源，适合中小企业，ee收费，适合大型公司。

参照：

- [GitLab Docker images | GitLab](https://docs.gitlab.com/ee/install/docker.html)
- [NGINX settings | GitLab](https://docs.gitlab.com/omnibus/settings/nginx.html#enable-https)

---

## 目录

[[_TOC_]]



## 1、安装Gitlab容器

### 1.1 使用`Docker Engine`安装

下载Gitlab

```bash
docker pull gitlab/gitlab-ce:latest
```

Gitlab安装运行指令
```bash
sudo docker run --detach --privileged \
  --hostname gitlab.example.com \
  --publish 8443:443 --publish 8080:80 --publish 8022:22 --publish 25:25 --publish 143:143 \
  --name gitlab \
  --restart always \
  --volume $HOME/gitlab/config:/etc/gitlab \
  --volume $HOME/gitlab/logs:/var/log/gitlab \
  --volume $HOME/gitlab/data:/var/opt/gitlab \
  --shm-size 256m \
  gitlab/gitlab-ce:latest
```

查询安装日志
```bash
sudo docker logs -f gitlab
```

查询root初始口令（Gitlab-ee版本，是浏览器第一次登录的时候，设置root口令）

```bash
# Gitlab-ce
sudo docker exec -it gitlab grep 'Password:' /etc/gitlab/initial_root_password

Password: WikgwLM91GPRDHYPLJvB9wvJa6qPqVmOGR/1cACC+60=
```

### 1.2 使用`Docker Compose`安装

- https://docs.gitlab.com/ee/install/docker.html#install-gitlab-using-docker-compose
- [安装步骤](file:///D:/%E6%8A%80%E8%A1%93%E8%B3%87%E6%96%99/Linux/Ubuntu/2022.03.21%20-%20Install%20GitLab%20using%20Docker%20Compose.html)

### 1.3 使用`Docker swarm mode`安装

- https://docs.gitlab.com/ee/install/docker.html#install-gitlab-using-docker-swarm-mode

## 2、安装PostFix邮件服务器

### 2.1 启动root权限docker会话

```bash
sudo docker exec -it gitlab /bin/bash
```

如果是第一次启动，需要安装如下软件包：

```bash
# 软件更新。
apt update
apt upgrade

# 安装编辑器
apt install nano

# 安装网络调试所需软件包。
apt install iputils-ping net-tools dnsutils

# 安装如下组件，目的是为了能够使用systemctl指令。
#apt install init
# 经过验证，下面的软件包无需安装，已经存在于init的软件包之内。
apt install systemctl
#apt install systemd

# 默认没有安装telnet，安装telnet包。
apt install telnet
```

### 2.2 安装PostFix邮件服务

- [Set up Postfix for incoming email | GitLab](https://docs.gitlab.com/ee/administration/reply_by_email_postfix_setup.html)

按照如下指令，安装PostFix包：

```bash
# Install the postfix package if it is not installed already:
# mail.example.com
apt update
apt install postfix
# 1. No configuration  <2. Internet Site>  3. Internet with smarthost  4. Satellite system  5. Local only
# General type of mail configuration: 2

# Install the mailutils package.
apt install mailutils

## Create user
# Create a user for incoming email.
useradd -m -s /bin/bash incoming
# Set a password for this user. <qwer1234>
passwd incoming

# Configure Postfix to use Maildir-style mailboxes:
postconf -e "home_mailbox = Maildir/"

# 检查PostFix状态，是否为启动状态？
postfix status
# 如果尚未启动Postfix，请启动PostFix
postfix start
# /etc/init.d/postfix restart
# service postfix restart

# 您还可以将 postfix 配置为在启动时启动：
systemctl enable postfix
```

### 2.3 向`incoming`用户发送邮件

Send the new `incoming` user an email to test SMTP, by entering the following into the SMTP prompt:

```bash
# 连接到本地 SMTP 服务器：
telnet localhost 25

# 按照如下指令发送邮件：
ehlo localhost
mail from: root@localhost
rcpt to: incoming@localhost
data
Subject: Re: Some issue

Sounds good!
.
quit
```

### 2.4 检查邮件是否送达？

❶切换用户到`incoming`

```
su - incoming
mail
```

❷应该能够看到如下内容，证明邮件已经正确送达。

```bash
"/var/mail/incoming": 1 message 1 unread
>U   1 root@localhost                           59/2842  Re: Some issue
```

❸退出邮件查看：

```bash
q
```

❹退出`incoming`用户登录，返回到`root`用户：

```bash
# exit 指令有一样的效果
logout
```

## 3、配置邮箱`Maildir`样式

说明：我们稍后安装它以添加 IMAP 身份验证，要求邮箱具有 `Maildir` 格式，而不是 `mbox`。

※ 因为在安装Postfix软件包的时候，已经设置为了Maildir格式，请图略本节内容。

### 3.1 配置邮箱样式为 `Maildir` 格式

❶将 Postfix 配置为使用 Maildir 样式的邮箱：

```bash
# /etc/init.d/postfix 文件尾部追加 "home_mailbox = Maildir/" 行
postconf -e "home_mailbox = Maildir/"
```

❷重新启动Postfix ：

```bash
/etc/init.d/postfix restart
```

❸参照[2.3](#向`incoming`用户发送邮件)发送一封邮件。

### 3.2 检查传入用户是否收到电子邮件

❶切换用户到`incoming`

```bash
su - incoming
MAIL=/home/incoming/Maildir
mail
```

❷应该看到这样的输出：

```bash
"/home/incoming/Maildir": 1 message 1 unread
>U   1 root@localhost                           59/2842  Re: Some issue
```

❸退出邮件应用程序：

```bash
q
```

❹退出`incoming`用户登录，返回到`root`用户：

```bash
logout
```



## 4、安装`IMAP`邮件服务器

### 4.1 安装`courier-imap`包

安装 courier-imap 包：

```bash
apt install courier-imap
# Create directories for web-based administration? [yes/no] no
```

※ 安装出现错误，用如下办法保证安装正常结束。

```bash
cd /var/lib/dpkg
mv info info.bak
mkdir info
apt update
apt upgrade
# 把info下面的文件拷贝到info.bak下面，本次处理在info目录下面只发现了一个format的文件。
cp info/format info.bak/format
mv info info.new
mv info.bak info
# 检测没有问题的话，可以删除info.new
rm info.new/format
rmdir info.new
```



并启动 imapd：

```bash
imapd start
```

### 4.2 启动`IMAP`服务
courier-authdaemon 安装后没有启动。 没有它，IMAP 身份验证将失败：

```bash
service courier-authdaemon start
```

您还可以将 courier-authdaemon 配置为在启动时启动：

```bash
systemctl enable courier-authdaemon
```



## 5、配置 Postfix 以接收`Internet`电子邮件

```bash
# 可以直接编辑这个文件，或者用后面的指令进行处理。
nano /etc/postfix/main.cf
```

```bash
# 让 Postfix 知道它应该考虑本地的域：
postconf -e "mydestination = gitlab.example.com, localhost.localdomain, localhost"

# 让 Postfix 知道它应该考虑作为 LAN 一部分的 IP：
# 假设 192.168.1.0/24 是您的本地 LAN。 如果您在同一本地网络中没有其他计算机，则可以安全地跳过此步骤。
postconf -e "mynetworks = 127.0.0.0/8, 172.17.0.0/24"

# 配置 Postfix 在所有接口上接收邮件，包括互联网：
postconf -e "inet_interfaces = all"

# 将 Postfix 配置为使用 + 分隔符进行子寻址：
postconf -e "recipient_delimiter = +"

# 重启Postfix：
service postfix restart
```



## 6、测试最终设置



### 6.1 在新设置下测试 SMTP

**❶发送邮件：**

```bash
# 连接到 SMTP 服务器：
telnet gitlab.example.com 25

# 您应该会看到如下提示：
Trying 123.123.123.123...
Connected to gitlab.example.com.
Escape character is '^]'.
220 gitlab.example.com ESMTP Postfix (Ubuntu)

# 如果您收到拒绝连接错误，请确保您的防火墙设置为允许端口 25 上的入站流量。

# 通过在 SMTP 提示符中输入以下内容，向传入用户发送电子邮件以测试 SMTP：
ehlo gitlab.example.com
mail from: root@gitlab.example.com
rcpt to: incoming@gitlab.example.com
data
Subject: Re: Some issue

Sounds good!
.
quit
```



**❷检查传入用户是否收到电子邮件**

如下指令：

```bash
# 切换到incoming用户
su - incoming
MAIL=/home/incoming/Maildir
mail

# 你应该看到这样的输出：
"/home/incoming/Maildir": 1 message 1 unread
>U   1 root@gitlab.example.com                           59/2842  Re: Some issue

# 退出邮件应用程序：
q

# 注销传入的帐户，然后恢复为 root：
logout
```



### 6.2 在新设置下测试 IMAP

如下指令：

```bash
# 连接到 IMAP 服务器：
telnet gitlab.example.com 143

# 您应该会看到如下提示：
Trying 123.123.123.123...
Connected to mail.gitlab.example.com.
Escape character is '^]'.
- OK [CAPABILITY IMAP4rev1 UIDPLUS CHILDREN NAMESPACE THREAD=ORDEREDSUBJECT THREAD=REFERENCES SORT QUOTA IDLE ACL ACL2=UNION] Courier-IMAP ready. Copyright 1998-2011 Double Precision, Inc.  See COPYING for distribution information.

# 通过在 IMAP 提示符中输入以下内容，以incoming用户身份登录以测试 IMAP：
# a login incoming PASSWORD
a login incoming qwer1234

# 将 PASSWORD 替换为您之前为传入用户设置的密码。
# 你应该看到这样的输出：
a OK LOGIN Ok.

# 断开与 IMAP 服务器的连接：
a logout
```

## ７．Docker内のサービスの自動起動設定

```bash
docker exec -it gitlab /bin/bash
nano /root/.bashrc

# 下記の内容を最後に追加する
postfix start
imapd start
service courier-authdaemon start
```

```bash
mkdir /home/.zeroops
nano /home/.zeroops/service.sh
chmod 755 /home/.zeroops/service.sh

/bin/bash /home/.zeroops/service.sh
```

```bash
#!/bin/bash
service postfix start
imapd start
service courier-authdaemon start
```

```bash
nano /etc/systemd/system/zeroops.service
chmod 755 /etc/systemd/system/zeroops.service

```

```bash
[Unit]
Description=ZeroOps mail service daemon
After=network.target
ConditionalPathExist=/home/.zeroops/

[Service]
ExecStart=//bin/bash /home/.zeroops/service.sh
Restart=no
Type=simple

[Install]
WantedBy=default.target 
```

```bash
systemctl enable zeroops.service
systemctl start zeroops.service
postfix status
imapd status
```

```bash
## 该指令加入HOST的启动参数里面，就可以保证DOCKER启动后，同时启动安装的服务。
## 但是该指令必须在DOCKER启动之后、执行才有效果。
docker exec -it gitlab /home/.zeroops/service.sh
```



## **完毕**

- 如果所有测试都成功，则 Postfix 已设置完毕并准备好接收电子邮件！ 继续使用传入的电子邮件指南来配置 GitLab。

```bash
sudo iptables -t nat -A DOCKER -p tcp --dport 25 -j DNAT --to-destination 172.17.0.2:25
sudo iptables -t nat -A DOCKER -p tcp --dport 143 -j DNAT --to-destination 172.17.0.2:143

# PING
apt install iputils-ping net-tools dnsutils

# 判断服务是否已经被设置为自动启动
systemctl is-enabled postfix
systemctl is-enabled courier-imap

# 再启动之后，需要启动如下服务（这个问题需要解决，应该跟着Docker启动一起启动）
docker exec -it gitlab /bin/bash
postfix start
imapd start
service courier-authdaemon start

nano /etc/systemd/system/postfix.service
nano /etc/systemd/system/gitlab.service
```



```bash
Download and install binary

# Download the binary for your system
sudo curl -L --output /usr/local/bin/gitlab-runner https://gitlab-runner-downloads.s3.amazonaws.com/latest/binaries/gitlab-runner-linux-amd64

# Give it permission to execute
sudo chmod +x /usr/local/bin/gitlab-runner

# Create a GitLab Runner user
sudo useradd --comment 'GitLab Runner' --create-home gitlab-runner --shell /bin/bash

# Install and run as a service
sudo gitlab-runner install --user=gitlab-runner --working-directory=/home/gitlab-runner
sudo gitlab-runner start

# Command to register runner

#sudo gitlab-runner register --url http://gitlab.example.com/ --registration-token $REGISTRATION_TOKEN
sudo gitlab-runner register --url http://gitlab.example.com/ --registration-token dT3SSMiP3NoR-7Jgj9ms
sudo gitlab-runner register --url http://192.168.114.169:8080/ --registration-token dT3SSMiP3NoR-7Jgj9ms
```





</font>