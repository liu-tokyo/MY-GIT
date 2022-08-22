## GIT 软件安装

## 1. Git客户端软件安装

官方网站：

- [Git - Book (git-scm.com)](https://git-scm.com/book/zh/v2)

### 1.1 GIT for Windows

- 启动命令提示符、或者是 **PowerShell** ， 输入如下命令：

  ```bash
  winget install --id Git.Git -e --source winget
  ```

  如果电脑上尚未安装 winget ，可以通过[官方网站](https://git-scm.com/download/win)下载安装。
  
  Git的代码在[Github](https://github.com/git-for-windows/git)上是公开的，你可以下载代码，然后自己编译。

### 1.2 GIT for Linux

请参照官网介绍：https://git-scm.com/download/linux

- Debian、Ubuntu、UOS等

  ```bash
  # sudo apt install git
  sudo apt install git-all
  ```

- Fedora

  ```bash
  sudo yum install git-all
  ```

### 1.3 Git for Mac

- 安装指令：

  ```bash
  # Download
  http://git-scm.com/download/mac
  
  # Install
  http://mac.github.com/
  ```

## 2. Git 环境设置

所有指令，在Linux环境，直接在终端内输入；Windows的话，在资源管理器的任意位置点击鼠标邮件，选择Git命令项目即可输入后续指令。

- 全局设置

  ```bash
  git config --global user.name "Administrator"
  git config --global user.email "admin@example.com"
  ```

  把用户名（`"Administrator"`）及邮件地址（`"admin@example.com"`），替换为自己的用户名、邮件地址。

- HTTPS认证设置

  如果远程克隆https的数据失败，出现如下错误：

  ```bash
  fatal: 无法访问 'https://gitlab.liu.com/liu-group/test1.git/'：server certificate verification failed. CAfile: none CRLfile: none
  ```

  可能是没有正确安装CA证书，执行如下指令即可：

  ```bash
  sudo apt install --reinstall ca-certificates
  ```

- 自建Gitlab服务器

  １．自建Gitlab服务器的证书是不被认可的，如果你足够信任该服务器，可以在`git clone`执行之前，先执行如下指令设置花茎变量：

  ```bash
  export GIT_SSL_NO_VERIFY=1
  ```

  一旦关闭终端，下次的话，仍然需要输入该指令。

  ２．如果想要一劳永逸的解决该问题，可是直接输入配置命令：

  ```bash
  git config --global http.sslVerify false
  ```

  这样等于少了一道SSL防护，虽然没有解决凭证问题，等于凭证放弃不管，对于不熟悉的服务器，不建议这么做。

## 3. Git指令操作

指令主要是为了熟悉Git的各种动作而已，普通用户最好是采用GUI进行操作。

### 3.1 常用指令

- Git仓库克隆：

  ```bash
  git clone https://github.com/tianqixin/runoob-git-test
  ```

  `https://github.com/tianqixin/runoob-git-test`为Git仓库的地址，你可以在Github页面、或者自建Gitlab上面找到相应的地址。

  如果克隆失败，检查地址是否正常、或者参照上一节的Git环境设置。
  
  软件开发人员，一般都是从分支进行克隆，而不是主分支。如果要可控分支：feature/test1
  
  ```bash
  git clone -b feature/test1 https://github.com/tianqixin/runoob-git-test
  ```
  
- 添加文件到暂存区

  ```bash
  git add .
  ```

  该指令是添加所有变化到暂存区，普通人用该指令就足够。但是软件开发人员，经常需要只添加必要文件，不需要的文件是不能随便更新到Git仓库的。

  需要用下面的指令查看到底有那些文件发生了变化：

  ```bash
  git status
  ```

  通过如下指令查看文件到底发生了什么改变：

  ```bash
  git diff
  ```

- 将暂存区内容添加到仓库中

  ```bash
  git commit -m "添加到远程"
  ```

  多人协作的话，一般都需要该指令之前，执行如下指令，查看是否远端已经发生了改变，防止发生冲突。

  ```bash
  git pull
  ```

- 上传远程代码并合并

  ```bash
  git push
  ```

  虽然后面可以加很多参数，可以有很复杂的操作；但是尽量只是用如上指令，也就是从哪里来的，再上传到哪里，别折腾。

### 3.2 其它指令

- 创建仓库命令

  | 命令        | 说明                                   | 备注 |
  | ----------- | -------------------------------------- | ---- |
  | `git init`  | 初始化仓库                             |      |
  | `git clone` | 拷贝一份远程仓库，也就是下载一个项目。 | 〇   |

- 提交与修改

  | 命令         | 说明                                     | 备注 |
  | ------------ | ---------------------------------------- | ---- |
  | `git add`    | 添加文件到暂存区                         | 〇   |
  | `git status` | 查看仓库当前的状态，显示有变更的文件。   | 〇   |
  | `git diff`   | 比较文件的不同，即暂存区和工作区的差异。 |      |
  | `git commit` | 提交暂存区到本地仓库。                   | 〇   |
  | `git reset`  | 回退版本。                               |      |
  | `git rm`     | 将文件从暂存区和工作区中删除。           |      |
  | `git mv`     | 移动或重命名工作区文件。                 |      |

- 提交日志

  | 命令               | 说明                                 | 备注 |
  | ------------------ | ------------------------------------ | ---- |
  | `git log`          | 查看历史提交记录                     |      |
  | `git blame <file>` | 以列表形式查看指定文件的历史修改记录 |      |

- 远程操作

  | 命令         | 说明               | 备注 |
  | ------------ | ------------------ | ---- |
  | `git remote` | 远程仓库操作       |      |
  | `git fetch`  | 从远程获取代码库   |      |
  | `git pull`   | 下载远程代码并合并 | 〇   |
  | `git push`   | 上传远程代码并合并 | 〇   |

参考：https://www.runoob.com/git/git-basic-operations.html

## 3. PowerShell 安装 (Windows)

如果是技术人员，建议在Windows上安装、使用PowerShell 超级终端，普通用户无此必要。

- 相关官方文档：

  [PowerShell ドキュメント - PowerShell | Microsoft Docs](https://docs.microsoft.com/ja-jp/powershell/)

- **从Misrosoft Store中查找 `PowerShell`的版本：**

  ```bash
  C:\WINDOWS\system32>winget search powershell
  'msstore' ソースを使用するには、使用する前に次の契約を表示する必要があります。
  Terms of Transaction: https://aka.ms/microsoft-store-terms-of-transaction
  ソースが正常に機能するには、現在のマシンの 2 文字の地理的リージョンをバックエンド サービスに送信する必要があります (例: "US")。
  
  すべてのソース契約条件に同意しますか?
  [Y] はい  [N] いいえ: Y
  名前                            ID                                  バージョン   一致            ソース
  --------------------------------------------------------------------------------------------------------
  PowerShell                      9MZ1SNWT0N5D                        Unknown                      msstore
  PowerShell Preview              9P95ZZKTNRN4                        Unknown                      msstore
  PowerShell Conference Asia 2015 9WZDNCRD37D8                        Unknown                      msstore
  PowerShell                      Microsoft.PowerShell                7.2.2.0                      winget
  Windows Terminal Preview        Microsoft.WindowsTerminal.Preview   1.13.10733.0 Tag: PowerShell winget
  Windows Terminal                Microsoft.WindowsTerminal           1.12.10732.0 Tag: powershell winget
  PowerShell Preview              Microsoft.PowerShell.Preview        7.3.0.3      Tag: powershell winget
  ConEmu                          Maximus5.ConEmu                     11.220.3080  Tag: powershell winget
  EasyConnect                     lstratman.easyconnect               3.1.0.105    Tag: powershell winget
  Oh My Posh                      JanDeDobbeleer.OhMyPosh             7.49.0       Tag: powershell winget
  TfsCmdlets                      Igoravl.TfsCmdlets                  2.2.1.2667   Tag: powershell winget
  electerm                        electerm.electerm                   1.19.5       Tag: powershell winget
  wol                             DarkfullDante.wol                   1.0.2        Tag: powershell winget
  AutomatedLab                    AutomatedLab.AutomatedLab           5.41.0       Tag: powershell winget
  PowerShell Universal            IronmanSoftware.PowerShellUniversal 2.6.2                        winget
  ```

- **安装PowerShell：**

  ```bash
  C:\Users\LIU VM>winget install Microsoft.PowerShell
  已找到 PowerShell [Microsoft.PowerShell] 版本 7.2.2.0
  此应用程序由其所有者授权给你。
  Microsoft 对第三方程序包概不负责，也不向第三方程序包授予任何许可证。
  Downloading https://github.com/PowerShell/PowerShell/releases/download/v7.2.2/PowerShell-7.2.2-win-x64.msi
    ██████████████████████████████   101 MB /  101 MB
  已成功验证安装程序哈希
  正在启动程序包安装...
  已成功安装
  ```

  
