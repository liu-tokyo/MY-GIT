# Gitlab 简易构筑

> https://qiita.com/AtsushiYokokawa/items/a43101664fca7ee11559



## 1. Gitlab 安装

- 执行以下命令，安装依赖包curl、openssh-server、ca-certificates：

  ```bash
  sudo apt-get update
  sudo apt-get install -y curl openssh-server ca-certificates
  ```

- 执行以下命令安装依赖包postfix：

  ```bash
  sudo apt-get install -y postfix
  ```

  由于这次我们不会配置邮件服务器，所以选择“无设置”。

- 执行以下命令将 GitLab 包添加到存储库：

  ```bash
  curl https://packages.gitlab.com/install/repositories/gitlab/gitlab-ce/script.deb.sh | sudo bash
  ```

- 运行以下命令来安装 GitLab 包：  
  （密码“hogehoge”被传递到GITLAB_ROOT_PASSWORD并设置密码的初始值。）

  ```bash
  sudo GITLAB_ROOT_PASSWORD="hogehoge" EXTERNAL_URL="http://192.168.137.61" apt install -y gitlab-ce
  ```

  注意：要按照自己的环境变更 `GITLAB_ROOT_PASSWORD` 和 `EXTERNAL_URL` 的实际数值。

## 2. 用户口令

### 2.1 初期口令

- 如果安装的时候，没有预置口令的话，安装结束之后，适用如下指令查询初期口令：  

  ```bash
  cat /etc/gitlab/initial_root_password
  ```

### 2.2 口令变更

- 登录Gitlab服务器并启动RilsConsole

  ```bash
  $ sudo gitlab-rails console -e production
  --------------------------------------------------------------------------------
   GitLab:       12.4.0 (1425a56c75b)
   GitLab Shell: 10.2.0
   PostgreSQL:   10.9
  --------------------------------------------------------------------------------
  Loading production environment (Rails 5.2.3)
  ```

- 从用户ID中获取用户（root为1）

  ```bash
  irb(main):001:0> user = User.find(1)
  => #<User id:1 @root>
  ```

  或者

  ```bash
  user = User.find_by(username: 'root')
  ## 打印对象的信息，确认一下
  pp user.attributes
  ```

  

- 设置密码。

  ```bash
  irb(main):007:0> user.password = 'password'
  => "password"
  irb(main):008:0> user.password_confirmation='password'
  => "password"
  ```

- 保存密码

  ```bash
  irb(main):011:0> user.save!
  Enqueued ActionMailer::DeliveryJob (Job ID: d21626c7-04f4-4068-80c0-e9fa1d1ffdfd) to Sidekiq(mailers) with arguments: "DeviseMailer", "password_change", "deliver_now", #<GlobalID:0x00007fbc78357c58 @uri=#<URI::GID gid://gitlab/User/1>>
  => true
  ```



## 3. 访问Gitlab

### 3.1 令牌访问

- 使用如下方式：

  ```
  https://oauth2:访问令牌@仓库地址
  http://oauth2:访问令牌@仓库地址
  ```

  

## 4. GitLab Mattermost 设置

> https://qiita.com/masakura/items/0a0f00dfdddc8ce27f29