## GIT 软件安装

- [Git - Book (git-scm.com)](https://git-scm.com/book/zh/v2)

### １．GIT for Windows

启动命令行，或者是 **PowerShell** ， 输入如下命令：

```bash
winget install --id Git.Git -e --source winget
```

也可以通过[官方网站](https://git-scm.com/download/win)下载安装。

### ２．GIT for Linux

Debian、Ubuntu、UOS等

```bash
# sudo apt install git
sudo apt install git-all
```

Fedora

```bash
sudo yum install git-all
```

### ３．Git for Mac

```bash
# Download
http://git-scm.com/download/mac

# Install
http://mac.github.com/
```





### ４．PowerShell 安装 (Windows)

在Windows上也要慢慢学会使用超级终端，尤其是对技术人员。

[PowerShell ドキュメント - PowerShell | Microsoft Docs](https://docs.microsoft.com/ja-jp/powershell/)

查找 **PowerShell**的版本：

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

安装PowerShell

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
