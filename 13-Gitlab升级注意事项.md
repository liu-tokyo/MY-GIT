# Gitlab升级注意事项

## 1. 逐步升级

- Gitlab服务器需要逐步升级，可以通过如下网址查询升级的所有步骤，不能直接升级到最新的版本。

  https://gitlab-com.gitlab.io/support/toolbox/upgrade-path/
  
  ※即使按照如上说法，也未必正确；需要在更新的时候看更新结果进行调整。
  
- 查询当前版本

  ```bash
  sudo gitlab-rake gitlab:env:info
  ```
  
- 升级过程中，可以通过远程桌面，或者 SSH 指令进行更新

  ```
  ssh vip@192.168.0.100
  ```

  用户名：`vip`

  口令：`qwer1234`



## 2. 升级指令

- 服务器升级的指令如下：

  ```bash
  # Step 1: Backup GitLab
  sudo gitlab-backup create
  
  # Step 2: Stop GitLab Services
  sudo gitlab-ctl stop
  
  # Step 3: Update Package Repository and Install New Version
  sudo apt-get update
  sudo apt-get install gitlab-ce=<new_version>
  
  # Step 4: Reconfigure GitLab
  sudo gitlab-ctl reconfigure
  
  # Step 5: Start GitLab Services
  sudo gitlab-ctl start
  
  # Step 6: Perform Post-Upgrade Checks
  sudo gitlab-rake gitlab:check
  ```

- 关机指令

  ```bash
  sudo shutdown now
  ```

  重启指令：

  ```bash
  sudo reboot now
  ```

- SSH 链接指令

  ```bash
  ssh vip@192.168.0.100
  ```

### 2.1 升级 - 2025.10.26

- 查询当前版本

  ```bash
  sudo gitlab-rake gitlab:env:info
  ```

  当前版本是 `17.10.1`

  ```bash
  vip@vip-OptiPlex-3020:~$ sudo gitlab-rake gitlab:env:info
  [sudo] vip 的密码：
  
  System information
  System:         Ubuntu 24.04
  Current User:   git
  Using RVM:      no
  Ruby Version:   3.2.5
  Gem Version:    3.6.5
  Bundler Version:2.6.5
  Rake Version:   13.0.6
  Redis Version:  7.0.15
  Sidekiq Version:7.2.4
  Go Version:     unknown
  
  GitLab information
  Version:        17.10.1
  Revision:       5ef58a00091
  Directory:      /opt/gitlab/embedded/service/gitlab-rails
  DB Adapter:     PostgreSQL
  DB Version:     14.17
  URL:            http://192.168.0.100
  HTTP Clone URL: http://192.168.0.100/some-group/some-project.git
  SSH Clone URL:  git@192.168.0.100:some-group/some-project.git
  Using LDAP:     no
  Using Omniauth: yes
  Omniauth Providers:
  
  GitLab Shell
  Version:        14.41.0
  Repository storages:
  - default:      unix:/var/opt/gitlab/gitaly/gitaly.socket
  GitLab Shell path:              /opt/gitlab/embedded/service/gitlab-shell
  
  Gitaly
  - default Address:      unix:/var/opt/gitlab/gitaly/gitaly.socket
  - default Version:      17.10.1
  - default Git Version:  2.48.1.gl1
  ```

  

- 查询升级步骤

  需要 `17.11.7` → `18.2.8` → `18.5.1` （最新版本）

- 升级指令

  ```bash
  ## 停止服务
  sudo gitlab-ctl stop
  
  ## 更新软件包仓库并安装新版本
  sudo apt-get update
  sudo apt-get install gitlab-ce=17.11.7-ce.0
  sudo apt-get install gitlab-ce=18.2.8-ce.0
  sudo apt-get install gitlab-ce=18.5.1-ce.0
  
  ## 重新配置 GitLab
  sudo gitlab-ctl reconfigure
  
  ## 启动服务
  sudo gitlab-ctl start
  
  ## 执行升级后检查
  sudo gitlab-rake gitlab:check
  ```

  实际升级过程中，直接升级 `17.11.7` 失败，尝试先升级到 `17.10.7`，好像没有问题。在如下页面查找到的版本信息：
  https://about.gitlab.com/releases/categories/releases/p2/

  ```bash
  sudo apt-get install gitlab-ce=17.10.7-ce.0
  sudo apt-get install gitlab-ce=17.11.0-ce.0
  sudo apt-get install gitlab-ce=17.11.7-ce.0
  sudo apt-get install gitlab-ce=18.2.8-ce.0
  sudo apt-get install gitlab-ce=18.5.1-ce.0
  
  ```

  每次更新之后，都需要重新启动电脑。如果是 SSH 链接的话，执行如下指令：

  ```bash
  sudo reboot now
  ```

  