

# 配置独立的邮件服务器

## 目录

[[_TOC_]]

## １．配置邮件服务器

### 1.1 运行容器

```bash
sudo docker run --detach --privileged -it \
  --hostname mail.example.com \
  --publish 25:25 --publish 110:110 --publish 143:143 \
  --name gitlab-mail \
  --restart always \
  ubuntu:latest
```

### 1.2 容器初始化处理

```bash
## 进入容器
docker exec -it gitlab-mail bash

## 软件更新。
apt update
apt upgrade

## 安装编辑器
apt install nano

## 安装网络调试所需软件包。
apt install iputils-ping net-tools dnsutils

## 安装如下组件，目的是为了能够使用systemctl指令。
#apt install init
## 经过验证，下面的软件包无需安装，已经存在于init的软件包之内。
#apt install systemctl
#apt install systemd

## 默认没有安装telnet，安装telnet包。
apt install telnet
```

### 1.3 安装PostFix邮件服务器

- [Set up Postfix for incoming email | GitLab](https://docs.gitlab.com/ee/administration/reply_by_email_postfix_setup.html)

```bash
## Install the postfix package if it is not installed already:
# mail.example.com
apt update
apt install postfix
# 1. No configuration  <2. Internet Site>  3. Internet with smarthost  4. Satellite system  5. Local only
# General type of mail configuration: 2

## Install the mailutils package.
apt install mailutils

#### Create user
## Create a user for incoming email.
useradd -m -s /bin/bash incoming
## Set a password for this user. <qwer1234>
passwd incoming

## Configure Postfix to use Maildir-style mailboxes:
postconf -e "home_mailbox = Maildir/"

## 检查PostFix状态，是否为启动状态？
postfix status
## 如果尚未启动Postfix，请启动PostFix
postfix start
# /etc/init.d/postfix restart
# service postfix restart

## 您还可以将 postfix 配置为在启动时启动：
#systemctl enable postfix
```



### 1.4 安装`IMAP`邮件服务器

```bash
## 安装 courier-imap 包：
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
## 把info下面的文件拷贝到info.bak下面，本次处理在info目录下面只发现了一个format的文件。
cp info/format info.bak/format
mv info info.new
mv info.bak info
## 检测没有问题的话，可以删除info.new
rm info.new/format
rmdir info.new
cd $HOME

```

启动imapd相关服务

```bash
## 并启动 imapd：
imapd start

## courier-authdaemon 安装后没有启动。 没有它，IMAP 身份验证将失败：
service courier-authdaemon start

## 您还可以将 courier-authdaemon 配置为在启动时启动：
# systemctl enable courier-authdaemon
```



### 1.5 配置 Postfix 以接收`Internet`邮件

```bash
## 可以直接编辑这个文件，或者用后面的指令进行处理。
# nano /etc/postfix/main.cf

## 让 Postfix 知道它应该考虑本地的域：
# postconf -e "mydestination = gitlab.example.com, localhost.localdomain, localhost"
postconf -e "mydestination = liu.com, localhost.localdomain, localhost"

## 让 Postfix 知道它应该考虑作为 LAN 一部分的 IP：
# 假设 192.168.1.0/24 是您的本地 LAN。 如果您在同一本地网络中没有其他计算机，则可以安全地跳过此步骤。
postconf -e "mynetworks = 127.0.0.0/8, 172.17.0.0/24"

## 配置 Postfix 在所有接口上接收邮件，包括互联网：
postconf -e "inet_interfaces = all"

## 将 Postfix 配置为使用 + 分隔符进行子寻址：
postconf -e "recipient_delimiter = +"

## 重启Postfix：
service postfix restart

```



### 1.6 在新设置下测试 SMTP

```bash
# 连接到 SMTP 服务器：
telnet mail.example.com 25

# 您应该会看到如下提示：
Trying 123.123.123.123...
Connected to mail.example.com.
Escape character is '^]'.
220 gitlab.example.com ESMTP Postfix (Ubuntu)

# 如果您收到拒绝连接错误，请确保您的防火墙设置为允许端口 25 上的入站流量。

# 通过在 SMTP 提示符中输入以下内容，向传入用户发送电子邮件以测试 SMTP：
ehlo mail.example.com
mail from: root@mail.example.com
rcpt to: incoming@mail.example.com
data
Subject: Re: Some issue

Sounds good!
.
quit
```

检查传入用户是否收到电子邮件

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

### 1.7 在新设置下测试 IMAP

```bash
# 连接到 IMAP 服务器：
telnet mail.example.com 143

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

### 1.8 Docker内のサービス起動

```bash
## 进入容器
docker exec -it gitlab-mail /bin/bash
#nano /root/.bashrc

## 启动如下内容
postfix start
imapd start
service courier-authdaemon start

```



## ２．制作专用安装镜像

### 2.1 先将容器保存为镜像

```bash
## 查看容器id及其状态
docker ps -a

## 若容器状态为running，需停止容器
docker stop gitlab-mail

## 将容器打包成镜像
docker commit gitlab-mail ubuntu:mail

## 查看保存下来的镜像
docker images

```

### 2.2 编写服务启动shell脚本

新建一个临时目录，进行后续的文件编辑。

- run.sh

```bash
#!/bin/bash
sleep 1
postfix start
imapd start
service courier-authdaemon start

```

### 2.3 编写Dockerfile文件

```bash
FROM ubuntu:mail
COPY run.sh /var/www/html/run.sh
RUN chmod +x /var/www/html/run.sh
WORKDIR /var/www/html
ENTRYPOINT /var/www/html/run.sh && tail -f /dev/null

```

### 2.4 重新构建ubuntu镜像

```bash
docker build -t ubuntu:liu . 

## 查看保存下来的镜像
docker images

```

### 2.5 使用重新构建的镜像启动容器

```bash
# docker run -itd -p 8888:80 -p 2222:22 --restart=always lamp:latest
sudo docker run --detach --privileged -it \
  --hostname mail.example.com \
  --publish 25:25 --publish 110:110 --publish 143:143 \
  --name gitlab-liu \
  --restart always \
  ubuntu:liu
 
## 查看容器id及其状态
docker ps -a

```

### 2.6 确认相关服务是否启动

```bash
docker exec -it gitlab-liu /bin/bash

# ps -ef | grep apache2
# ps -ef | grep mysql
postfix status
imapd status

```

### 2.7 用固定IP地址启动邮件容器

如果能够分配固定地址的话，可能配置起来更加简单，请参照如下语法：

```bash
sudo docker run --detach \
  --hostname mail.example.com \
  --publish 198.51.100.1:443:443 \
  --publish 198.51.100.1:80:80 \
  --publish 198.51.100.1:22:22 \
  --name gitlab \
  --restart always \
  --volume $GITLAB_HOME/config:/etc/gitlab \
  --volume $GITLAB_HOME/logs:/var/log/gitlab \
  --volume $GITLAB_HOME/data:/var/opt/gitlab \
  --shm-size 256m \
  gitlab/gitlab-ee:latest
```



## ３．Gitlab容器内部配置

### 3.1 配置邮件服务器Domain

因为没有真实的邮件Domian，属于内部使用的邮件服务器，需要设置 hosts 文件，来让设备确定邮件服务器的IP地址：

```bash
## 编辑 hosts 文件
nano /etc/hosts

## 增加一条记录 192.168.114.169 是HOST电脑的IP，邮件服务器的相关端口公开在这个IP上。
192.168.114.169 mail.example.com
```

### 3.2 给Gitlab登录一个Gitlab-runner

工作内容是让Gitlab发生的pipeline错误，发送邮件到相应的邮件服务器，为了能够产生错误，必须为新建的Gitlab网站注册一个Gitlab-runner。

### 3.3 启动runner容器

新建一个Ubuntu容器，在该容器上安装Gitlab-runner。

```bash
## 新建一个Ubuntu的容器，名字为 runner。
sudo docker run -itd --privileged \
  --hostname runner.example.com \
  --name runner \
  --restart always \
  --shm-size 256m \
  ubuntu:latest
  
```

共享runner注册用信息：

```bash
## 查询Gitlab，找到runner注册的信息
## 在管理区域查找，注册一个共享runner
# http://192.168.114.169:8080

## Register the runner with this URL: 
http://192.168.114.169:8080/
## And this registration token: 
i4-6UHhGDVi6enZ5KRhs

export REGISTRATION_TOKEN=i4-6UHhGDVi6enZ5KRhs
```

### 3.4 下载、安装Gitlab-runner

```bash
## 进入容器
docker exec -it runner bash
apt update
apt upgrade
apt install wget

## Download the binary for your system
# sudo curl -L --output /usr/local/bin/gitlab-runner https://gitlab-runner-downloads.s3.amazonaws.com/latest/binaries/gitlab-runner-linux-amd64

## 实际上是用wget下载的
wget https://gitlab-runner-downloads.s3.amazonaws.com/latest/binaries/gitlab-runner-linux-amd64
cp gitlab-runner-linux-amd64 /usr/local/bin/gitlab-runner

## Give it permission to execute
chmod +x /usr/local/bin/gitlab-runner

## Create a GitLab Runner user
useradd --comment 'GitLab Runner' --create-home gitlab-runner --shell /bin/bash

## Install and run as a service
gitlab-runner install --user=gitlab-runner --working-directory=/home/gitlab-runner
gitlab-runner start

# gitlab-runner -version
# gitlab-runner status
```

### 3.5 注册Gitlab-runner

```bash
## 172.17.0.1 HOST的IP
gitlab-runner register --url http://192.168.114.169:8080/ --registration-token $REGISTRATION_TOKEN
gitlab-runner register --url http://172.17.0.1:8080/ --registration-token $REGISTRATION_TOKEN

```

注意：这是一个容器启动，所以服务不会随着Docker启动，一起启动，需要进入容器，执行如下指令：

```bash
```

否则Gitlab的pipeline一直是pending状态，因为runner并没有正常启动。

[Gitlab runner status show "the service is not installed" but it was installed and run successfully - How to Use GitLab - GitLab Forum](https://forum.gitlab.com/t/gitlab-runner-status-show-the-service-is-not-installed-but-it-was-installed-and-run-successfully/60381)

[How to Install GitLab Runner on Ubuntu 20.04 LTS (fosstechnix.com)](https://www.fosstechnix.com/how-to-install-gitlab-runner-on-ubuntu/)