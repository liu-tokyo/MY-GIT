# Gitlab安装及调试



## 1. Gitlab安装

### 1.1 安装和配置必要的依赖项

- 依赖项安装指令

  ```bash
  sudo apt-get update
  sudo apt-get install -y curl openssh-server ca-certificates tzdata perl
  ```

- 邮件服务器安装指令

  接下来，安装 Postfix 以发送通知电子邮件。 如果您想使用其他解决方案发送电子邮件，请跳过此步骤并在安装 GitLab 后配置外部 SMTP 服务器。

  ```bash
  sudo apt-get install -y postfix
  
  ## 使用私人域名
  liu.com
  ```
  
  在 Postfix 安装期间，可能会出现一个配置屏幕。 选择“Internet 站点”并按 Enter。 使用您服务器的外部 域名 作为“邮件名称”，然后按 Enter。 如果出现其他屏幕，请继续按 Enter 键接受默认设置。

### 1.2 添加 GitLab 包存储库并安装包

- 包存储库

  ```bash
  ## 方法1： - 参照：https://www.gitlab.jp/install/#ubuntu
  curl https://packages.gitlab.com/install/repositories/gitlab/gitlab-ee/script.deb.sh | sudo bash
  curl https://packages.gitlab.com/install/repositories/gitlab/gitlab-ce/script.deb.sh | sudo bash
  ## 方法2： - 参照：https://packages.gitlab.com/gitlab/gitlab-ee/install
  curl -s https://packages.gitlab.com/install/repositories/gitlab/gitlab-ee/script.deb.sh | sudo bash
  curl -s https://packages.gitlab.com/install/repositories/gitlab/gitlab-ce/script.deb.sh | sudo bash
  ```

- 修改软件源
  
  打开文件
  
  ```bash
  ## 专业版
  sudo nano /etc/apt/sources.list.d/gitlab_gitlab-ee.list
  ## 普通版
  sudo nano /etc/apt/sources.list.d/gitlab_gitlab-ce.list
  ```
  
  把文件中的 `jammy` 修改为 `xenial`    
  ※ 为什么必须把`jammy`修改为`xenial`呢？只有修改之后，下一步【添加软件源】的时候，才不会显示错误。
  
- 添加软件源

  【软件和更新】→【其它软件】点击【添加】按钮添加软件源：
  
  ```bash
  ## 专业版
  deb https://packages.gitlab.com/gitlab/gitlab-ee/ubuntu/ xenial main
  deb-src https://packages.gitlab.com/gitlab/gitlab-ee/ubuntu/ xenial main
  ## 普通版
  deb https://packages.gitlab.com/gitlab/gitlab-ce/ubuntu/ xenial main
  deb-src https://packages.gitlab.com/gitlab/gitlab-ce/ubuntu/ xenial main
  ```
  
  添加软件源之后，`/etc/apt/sources.list.d/`目录会多出来几个配置文件，会导致定义重复的警告，需要删除不需要的文件：
  
  - 专业版
  
    `/etc/apt/sources.list.d/gitlab_gitlab-ee.list` 的内容拷贝到 `archive_uri-https_packages_gitlab_com_gitlab_gitlab-ee_ubuntu_-jammy.list`
  
  - 普通版
  
    `/etc/apt/sources.list.d/gitlab_gitlab-ce.list` 的内容拷贝到 `archive_uri-https_packages_gitlab_com_gitlab_gitlab-ce_ubuntu_-jammy.list`
  
  软后删除如下文件：
  
  ```bash
  cd /etc/apt/sources.list.d/
  ## 专业版
  sudo rm archive_uri-https_packages_gitlab_com_gitlab_gitlab-ee_ubuntu_-jammy.list.save
  sudo rm /etc/apt/sources.list.d/gitlab_gitlab-ee.list
  sudo rm /etc/apt/sources.list.d/gitlab_gitlab-ee.list.save
  ## 普通版
  sudo rm archive_uri-https_packages_gitlab_com_gitlab_gitlab-ce_ubuntu_-jammy.list.save
  sudo rm /etc/apt/sources.list.d/gitlab_gitlab-ce.list
  sudo rm /etc/apt/sources.list.d/gitlab_gitlab-ce.list.save
  ```
  
  ※ 为啥文件名称里面有 `jammy` ，但是内容里面的 `jammy` 必须修改为 `xenial` 呢？
  
- 安装软件包

  接下来，安装 GitLab 包。 确保您已正确设置 域名，并将 https://gitlab.example.com 更改为您要访问 GitLab 实例的 URL。 安装将在该 URL 自动配置和启动 GitLab。
  对于 https:// URL，GitLab 将自动使用 Let's Encrypt 请求证书，这需要入站 HTTP 访问和有效的主机名。 您也可以使用自己的证书或只使用 http:// （不带 s ）。
  如果您想为初始管理员用户 (root) 指定自定义密码，请查看文档。 如果没有指定密码，会自动生成一个随机密码。

  ```bash
  #### 方法1： - 参照：https://www.gitlab.jp/install/#ubuntu
  ## 专业版
  sudo EXTERNAL_URL="https://gitlab.liu.com" apt-get install gitlab-ee
  ## 普通版
  sudo EXTERNAL_URL="https://gitlab.liu.com" apt-get install gitlab-ce
  
  #### 方法2：
  ## 专业版
  sudo apt-get install gitlab-ee
  ## 普通版
  sudo apt-get install gitlab-ce
  ```
  
  ※这一步安装需要花费几分钟时间，需要有点儿耐心。
  
### 1.3 浏览到主机名并登录

除非您在安装期间提供了自定义密码，否则将随机生成密码并在 `/etc/gitlab/initial_root_password` 中存储 24 小时。 使用此密码和用户名 root 登录。
有关安装和配置的详细说明，请参阅我们的文档。

- 使用以下命令检查 IP 地址：

  ```bash
  ip a
  ```

- 设置HOST

  ```bash
  sudo nano /etc/hosts
  ```
  
- 在浏览器里面输入如下URL：
  
  ```bash
  https://192.168.114.142/
  https://192.168.114.146/
  https://gitlab.liu.com
  ```

> 首次登录需要设置root用户的口令：65qifxDt44sEMgS

- 设置显示语言

  初始状态是英文，修改为中文，至少看起来比较容易一些。

  【Preferences】→【Localization】，选择【简体中文】；显示有72%被翻译了，也就是说还有不少英文存在。

### 1.4 设置您的通信偏好

访问我们的电子邮件订阅偏好中心，让我们知道何时与您沟通。 我们有明确的电子邮件选择加入政策，因此您可以完全控制我们向您发送电子邮件的内容和频率。
我们每月两次发送您需要了解的 GitLab 新闻，包括新功能、集成、文档和我们开发团队的幕后故事。 有关与错误和系统性能相关的重要安全更新，请订阅我们的专用安全通讯。

> 重要提示：如果您不选择加入安全通讯，您将不会收到安全警报。

### 1.5 建议的后续步骤

完成安装后，请考虑推荐的[后续步骤](https://docs.gitlab.com/ee/install/next_steps.html)，包括身份验证选项和注册限制。

- 设置主机名

  ```bash
  vi /etc/gitlab/gitlab.rb
  external_url 'http://gitlab.example.com'
  ## *默认为“http://gitlab.example.com”
  ## *改不改都没关系。
  ```

- 反映/etc/gitlab/gitlab.rb的配置文件。

  ```bash
  ## 重新启动Gitlab
  sudo gitlab-ctl reconfigure
  
  ## 重新启动nginx服务
  sudo gitlab-ctl restart nginx
  
  ## 查询Gitlab状态
  sudo gitlab-ctl status
  ```

- 个人口令

  | Gitlab-ID | 邮件地址                 | 口令            | 备注 |
  | --------- | ------------------------ | --------------- | ---- |
  | root      |                          | 65qifxDt44sEMgS |      |
  | liu       | `zhijun_liu@hotmail.com` | `Zliu5186`      |      |

### 1.6 使用个人邮件注册用户

- Gitlag用户注册

  个人用另外一台Debian11.4的电脑进行用户注册。

  1.打开终端，设置Gitlab服务器的域名

  ```bash
  sudo nano /etc/hosts
  ```

  2.浏览器的地址栏输入如下地址：（会出现安全警告，是因为个人证书不被浏览器认可的原因）

  ```bash
  https://gitlab.liu.com
  ```

  3.显示Gitlab的首页，注册个人用户。

- Gitlab认证用户

  回到Gitlab服务器端，当然可以在任意电脑上操作，只要你知道root用户的口令。

  【管理中心】→【仪表板】→【最新用户】，知道自己的名字，进入画面后，【批准用户】即可。

- 新用户登录

  重新回到个人用户登录的电脑，进入Gitlab的首页，输入用户名、口令之后，就能正常登录到Gitlab。

## 2. Debian11上安装Git客户端

参照URL：https://installati.one/debian/11/git/

### 2.0 设置HOST

> 因为是本地安装的Gitlab，并没有真正的域名，所以需要在客户端的电脑里面设置*gitlab.example.com*的IP地址。

- 编辑 /etc/hosts 文件 （Debian，Ubuntu）

  ```bash
  $ sudo nano /etc/hosts
  127.0.0.1       localhost
  127.0.1.1       debian
  192.168.114.146 gitlab.example.com
  
  # The following lines are desirable for IPv6 capable hosts
  ::1     localhost ip6-localhost ip6-loopback
  ff02::1 ip6-allnodes
  ff02::2 ip6-allrouters
  ```

  根据Gitlab服务器的实际IP，追加1行`192.168.114.146 gitlab.example.com`。

### 2.1 安装Git客户端

- 使用以下命令使用 apt-get 更新 apt 数据库。

  ```bash
  sudo apt update
  ```

- 更新 apt 数据库后，我们可以通过运行以下命令使用 apt-get 安装 git：

  ```bash
  sudo apt -y install git
  ```

- 显示Git客户端版本

  ```bash
  git --version
  ```

- 要仅卸载 git 包，我们可以使用以下命令：

  ```bash
  sudo apt remove git
  ```

- 要卸载 Debian 11 不再需要的 git 及其依赖项，我们可以使用以下命令：

  ```bash
  sudo apt -y autoremove git
  ```

- 要从 Debian 11 中删除 git 配置和数据，我们可以使用以下命令：

  ```bash
  sudo apt -y purge git
  ```

- 删除 git 配置、数据及其所有依赖项

  ```bash
  sudo apt -y autoremove --purge git
  ```

### 2.2 配置Git客户端

参照官网：https://git-scm.com/book/zh/v2/%E8%B5%B7%E6%AD%A5-%E5%88%9D%E6%AC%A1%E8%BF%90%E8%A1%8C-Git-%E5%89%8D%E7%9A%84%E9%85%8D%E7%BD%AE

- 可以通过以下命令查看所有的配置以及它们所在的文件：

  ```bash
  git config --list --show-origin
  ```

- 用户信息

  ```bash
  git config --global user.email "zhijun_liu@hotmail.com"
  git config --global user.name "liu zhijun"
  ```

  ※Debian：执行后，/home/liu/.gitconfig 文件中会保存该设置内容。

- 检查配置信息

  如果想要检查你的配置，可以使用 `git config --list` 命令来列出所有 Git 当时能找到的配置。

  ```bash
  $ git config --list
  user.name=John Doe
  user.email=johndoe@example.com
  color.status=auto
  color.branch=auto
  color.interactive=auto
  color.diff=auto
  ...
  ```

  你可能会看到重复的变量名，因为 Git 会从不同的文件中读取同一个配置（例如：`/etc/gitconfig` 与 `~/.gitconfig`）。 这种情况下，Git 会使用它找到的每一个变量的最后一个配置。

- 你可以通过输入 `git config <key>`： 来检查 Git 的某一项配置

  ```bash
  $ git config user.name
  John Doe
  ```


### 2.3 生成 SSH 密钥对

如果您没有现有的 SSH 密钥对，请生成一个新的。

1. 打开一个终端。

2. 键入`ssh-keygen -t`后跟键类型和可选注释。此注释包含在`.pub`创建的文件中。您可能希望使用电子邮件地址发表评论。

   例如，对于 ED25519：

   ```bash
   ssh-keygen -t ed25519 -C "<comment>"
   ```

3. 按 Enter。显示类似于以下的输出：

   ```bash
   Generating public/private ed25519 key pair.
   Enter file in which to save the key (/home/user/.ssh/id_ed25519):
   ```

4. 接受建议的文件名和目录，除非您正在生成[部署密钥](../user/project/deploy_keys/index.md) 或想要保存在存储其他密钥的特定目录中。

   您还可以将 SSH 密钥对专用于[特定主机](#configure-ssh-to-point-to-a-different-directory)。

5. 指定[密码](https://www.ssh.com/ssh/passphrase/)：

   ```bash
   Enter passphrase (empty for no passphrase):
   Enter same passphrase again:
   ```
   
   ※本来以为这里输入口令没啥意义，如果有密钥的话，确实用不到；但是如果是另外的电脑需要克隆Gitlab的项目的话，需要用到这里设置的密码。

6. 将显示确认信息，包括有关文件存储位置的信息。

   ```bash
   ...
   Your identification has been saved in /home/liu/.ssh/id_ed25519
   Your public key has been saved in /home/liu/.ssh/id_ed25519.pub
   The key fingerprint is:
   SHA256:/Qw+5cTtF3NJCuGadttyQWT2pgr9ScGATe4ak3Sl/1I <comment>
   The key's randomart image is:
   +--[ED25519 256]--+
   ...
   ```

7. 查看公钥的内容

   ```bash
   nano /home/liu/.ssh/id_ed25519.pub
   ```

   该内容拷贝后，更新到Gitlab服务器，这样就可以通过SSH来操作数据。

生成公钥和私钥。 [将公共 SSH 密钥添加到您的 GitLab 帐户](#add-an-ssh-key-to-your-gitlab-account)并确保私钥安全。

### 2.4 将 SSH 配置为指向不同的目录

如果您没有将 SSH 密钥对保存在默认目录中，请将您的 SSH 客户端配置为指向存储私钥的目录。

1. 打开终端并运行以下命令：

   ```bash
   eval $(ssh-agent -s)
   ## ssh-add <directory to private SSH key>
   ssh-add /home/liu/.ssh/id_ed25519
   ```

2. 将这些设置保存在`~/.ssh/config`文件中。例如：

   ```bash
   # GitLab.com
   Host gitlab.com
     PreferredAuthentications publickey
     IdentityFile ~/.ssh/gitlab_com_rsa
   
   # Private GitLab instance
   Host gitlab.company.com
     PreferredAuthentications publickey
     IdentityFile ~/.ssh/example_com_rsa
   ```

   有关这些设置的更多信息，请参阅[`man ssh_config`](https://man.openbsd.org/ssh_config)SSH 配置手册中的页面。
   
   针对个人的Gitlab情况：
   
   ```bash
   # gitlab.liu.com
   Host gitlab.liu.com
     PreferredAuthentications publickey
     IdentityFile ~/.ssh/id_ed25519
   ```
   

公共 SSH 密钥对于 GitLab 必须是唯一的，因为它们绑定到您的帐户。当您使用 SSH 推送代码时，您的 SSH 密钥是您拥有的唯一标识符。它必须唯一地映射到单个用户。

### 2.5 更新您的 SSH 密钥密码

您可以更新 SSH 密钥的密码。

- 打开终端并运行以下命令：

  ```bash
  ssh-keygen -p -f /path/to/ssh_key
  ```

- 在提示符下，键入密码并按 Enter。

**将您的 RSA 密钥对升级为更安全的格式**

如果您的 OpenSSH 版本介于 6.5 和 7.8 之间，您可以将私有 RSA SSH 密钥保存为更安全的 OpenSSH 格式。

- 打开终端并运行以下命令：

  ```bash
  ssh-keygen -o -f ~/.ssh/id_rsa
  ```

  或者，您可以使用以下命令生成具有更安全加密格式的新 RSA 密钥：

  ```bash
  ssh-keygen -o -t rsa -b 4096 -C "<comment>"
  ```


### 2.6 将 SSH 密钥添加到您的 GitLab 帐户

要将 SSH 与 GitLab 一起使用，请将您的公钥复制到您的 GitLab 帐户。

- 复制您的公钥文件的内容。您可以手动执行此操作或使用脚本。例如，要将 ED25519 密钥复制到剪贴板：

  苹果系统：

  ```bash
  tr -d '\n' < ~/.ssh/id_ed25519.pub | pbcopy
  ```

  Linux（需要xclip软件包）：

  ```bash
  xclip -sel clip < ~/.ssh/id_ed25519.pub
  ```

  Windows 上的 Git Bash：

  ```bash
  cat ~/.ssh/id_ed25519.pub | clip
  ```

  替换`id_ed25519.pub`为您的文件名。例如，`id_rsa.pub`用于 RSA。

- 登录 GitLab。

- 在右上角，选择您的头像。

- 选择**首选项**。

- 从左侧边栏中，选择**SSH Keys**。

- 在“**密钥**”框中，粘贴您的公钥的内容。如果您手动复制了密钥，请确保复制整个密钥，该密钥以`ssh-ed25519`or开头`ssh-rsa`，并且可能以注释结尾。

- 在**标题**文本框中，输入描述，例如*Work Laptop*或 *Home Workstation*。

- 可选的。在“**到期**日期”框中，选择到期日期。[（在GitLab 12.9](https://gitlab.com/gitlab-org/gitlab/-/issues/36243)中引入。）到期日期仅供参考，不会阻止您使用密钥。但是，管理员可以查看到期日期并将其用作[删除密钥](../user/admin_area/credentials_inventory.md#delete-a-users-ssh-key)时的指导。

  - GitLab 每天 02:00 AM UTC 检查所有 SSH 密钥。它会通过电子邮件发送在当前日期到期的所有 SSH 密钥的到期通知。（在 GitLab 13.11 中[引入。）](https://gitlab.com/gitlab-org/gitlab/-/issues/322637)
  - GitLab 每天 01:00 AM UTC 检查所有 SSH 密钥。它会通过电子邮件发送所有计划在 7 天后到期的 SSH 密钥的到期通知。（在 GitLab 13.11 中[引入。）](https://gitlab.com/gitlab-org/gitlab/-/issues/322637)

- 选择**添加键**。

### 2.7 验证您是否可以连接

验证您的 SSH 密钥是否已正确添加。

1. 对于 GitLab.com，为确保您连接到正确的服务器，请确认 [SSH 主机密钥指纹](../user/gitlab_com/index.md#ssh-host-keys-fingerprints)。

2. 打开终端并运行此命令，替换`gitlab.example.com`为您的 GitLab 实例 URL：

   ```bash
   ssh -T git@gitlab.liu.com
   ssh -T git@192.168.114.146
   ```

3. 如果这是您第一次连接，您应该验证 GitLab 主机的真实性。如果您看到如下消息：

   ```bash
   The authenticity of host 'gitlab.example.com (35.231.145.151)' can't be established.
   ECDSA key fingerprint is SHA256:HbW3g8zUjNSksFbqTiUWPWg2Bq1x8xdGUrliXFzSnUw.
   Are you sure you want to continue connecting (yes/no)? yes
   Warning: Permanently added 'gitlab.example.com' (ECDSA) to the list of known hosts.
   ```

   键入`yes`并按 Enter。

   ```bash
   iu@debian:~/.ssh$ ssh -T git@gitlab.liu.com
   The authenticity of host 'gitlab.liu.com (192.168.114.146)' can't be established.
   ECDSA key fingerprint is SHA256:3RlEhtcCoqB00DGgCfLFW3ArmP/tTbKBKbY3HZd+7Fk.
   Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
   Warning: Permanently added 'gitlab.liu.com,192.168.114.146' (ECDSA) to the list of known hosts.
   Welcome to GitLab, @liu!
   ```

4. 再次运行该`ssh -T git@gitlab.example.com`命令。您应该收到*欢迎来到 GitLab `@username`，！*信息。

   如果没有出现欢迎消息，您可以通过`ssh` 在详细模式下运行来进行故障排除：

   ```bash
   ssh -Tvvv git@gitlab.liu.com
   ```

*确认可以正常连接。*

### 2.8 测试Git动作 - SSH

创建一个名字为MY-GIT的空白项目（Project），测试是否可以正常CLONE、以及COMMIT、PUSH等。

- CLONE - 

  ```bash
  liu@debian:~/MY-GIT$ git clone git@gitlab.example.com:liu/my-git.git
  正克隆到 'my-git'...
  The authenticity of host 'gitlab.example.com (192.168.114.146)' can't be established.
  ECDSA key fingerprint is SHA256:0eCM22ZbdXqP+hKb7nzz7NVMBJfXalUk8cWLhGVt48o.
  Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
  Warning: Permanently added 'gitlab.example.com' (ECDSA) to the list of known hosts.
  warning: 您似乎克隆了一个空仓库。
  ```

- **COMMIT - 将更新写入暂存区**

  建立一个README.MD的文件写入任意内容。

  ```bash
  liu@debian:~/MY-GIT/my-git$ git add .
  liu@debian:~/MY-GIT/my-git$ git commit -m test01
  [master（根提交） 7ae4218] test01
   1 file changed, 44 insertions(+)
   create mode 100644 README.md
  ```

  因为是测试，所以直接提交给了主分支。

- PUSH

  ```bash
  liu@debian:~/MY-GIT/my-git$ git push
  枚举对象中: 3, 完成.
  对象计数中: 100% (3/3), 完成.
  使用 4 个线程进行压缩
  压缩对象中: 100% (2/2), 完成.
  写入对象中: 100% (3/3), 635 字节 | 635.00 KiB/s, 完成.
  总共 3（差异 0），复用 0（差异 0），包复用 0
  To gitlab.example.com:liu/my-git.git
   * [new branch]      master -> master
  ```

  数据正常上传到了Gitlab服务器。

### 2.9 测试Git动作 - HTTPS

- CLONE

  ```bash
  liu@debian:~/MY-GIT/test$ git clone https://gitlab.example.com/liu/my-git.git
  正克隆到 'my-git'...
  fatal: 无法访问 'https://gitlab.example.com/liu/my-git.git/'：server certificate verification failed. CAfile: none CRLfile: none
  ```

  是否使用SSH之后，HTTPS就不再可以使用？应该不会，还是设置存在问题。

### ★ 其它

- https://packages.gitlab.com/gitlab/gitlab-ee/install

  ```bash
  sudo nano /etc/apt/sources.list.d/gitlab_gitlab-ee.list
  sudo nano /etc/apt/sources.list.d/gitlab_gitlab-ce.list
  
  sudo gedit /etc/apt/sources.list.d/gitlab_gitlab-ee.list
  sudo gedit /etc/apt/sources.list.d/gitlab_gitlab-ce.list
  ```

  ```bash
  deb https://packages.gitlab.com/gitlab/gitlab-ee/ubuntu/ xenial main
  deb-src https://packages.gitlab.com/gitlab/gitlab-ee/ubuntu/ xenial main
  ```

  ```bash
  deb https://packages.gitlab.com/gitlab/gitlab-ce/ubuntu/ xenial main
  deb-src https://packages.gitlab.com/gitlab/gitlab-ce/ubuntu/ xenial main
  ```

  ```bash
  deb [signed-by=/usr/share/keyrings/gitlab_gitlab-ee-archive-keyring.gpg] https://packages.gitlab.com/gitlab/gitlab-ee/ubuntu/ jammy main
  deb-src [signed-by=/usr/share/keyrings/gitlab_gitlab-ee-archive-keyring.gpg] https://packages.gitlab.com/gitlab/gitlab-ee/ubuntu/ jammy main
  ```

  ```bash
  deb [signed-by=/usr/share/keyrings/gitlab_gitlab-ee-archive-keyring.gpg] https://packages.gitlab.com/gitlab/gitlab-ee/ubuntu/ xenial main
  deb-src [signed-by=/usr/share/keyrings/gitlab_gitlab-ee-archive-keyring.gpg] https://packages.gitlab.com/gitlab/gitlab-ee/ubuntu/ xenial main
  ```

  ```bash
  deb [signed-by=/usr/share/keyrings/gitlab_gitlab-ee-archive-keyring.gpg] https://packages.gitlab.com/gitlab/gitlab-ce/ubuntu/ xenial main
  deb-src [signed-by=/usr/share/keyrings/gitlab_gitlab-ee-archive-keyring.gpg] https://packages.gitlab.com/gitlab/gitlab-ce/ubuntu/ xenial main
  ```

