# 提高国内访问 GitHub 的速度

## 1. GitHub 镜像访问

最常用的镜像地址：

- https://github.com.cnpmjs.org

  个人测试不可用
- https://hub.fastgit.org

  个人测试可用

也就是说上面的镜像就是一个克隆版的 GitHub，你可以访问上面的镜像网站，网站的内容跟 GitHub 是完整同步的镜像，然后在这个网站里面进行下载克隆等操作。



## 2. 修改静态域名

通过修改 HOSTS 文件进行加速，需要手动把cdn和ip地址绑定。

1. 获取 github 的 global.ssl.fastly 地址

   访问：http://github.global.ssl.fastly.net.ipaddress.com/#ipinfo 获取cdn和ip域名：

   ![img](http://mianbaoban-assets.oss-cn-shenzhen.aliyuncs.com/xinyu-images/MBXY-CR-bb11a7b3c76aa09f3ea0b5602ef4fb2f.png)

   得到：199.232.69.194 https://github.global.ssl.fastly.net

2. 获取github.com地址

   访问：https://github.com.ipaddress.com/#ipinfo 获取cdn和ip：

   ![img](http://mianbaoban-assets.oss-cn-shenzhen.aliyuncs.com/xinyu-images/MBXY-CR-a0c5e04950073a43b56d8efcbe9a8a74.png)

   得到：140.82.114.4 http://github.com

3. 修改 host 文件映射上面查找到的 IP

   用管理者权限打开命令提示符，输入如下指令：

   ```
   notepad C:\Windows\System32\drivers\etc\hosts
   ```

   文件末尾处添加如下内容：

   ```
   199.232.69.194 github.global.ssl.fastly.net
   140.82.114.4 github.com
   ```

   ※IP地址要按照真实取得到的IP设置，不同地域，取得的IP会有所不同。

