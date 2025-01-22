# Git正常拉取Tortoisegit无法拉取

## 1. 原因

- 出现这种问题的原因是因为安装的 TortoiseGit 使用由于TortoiseGit的默认网络SSH client是 TortoiseGitPlink.exe，如下图  
  ![image-20250123001035342](images/image-20250123001035342.png)

## 2. 解决办法

- 首先进行TortoiseGit设置  
  ![image-20250123001213333](images/image-20250123001213333.png)
- 将路径改为 `Git` 的相应路径即可  
  ![image-20250123001256063](images/image-20250123001256063.png)
