# Gitlab家庭版优化

在 8GB 内存的 Ubuntu 上运行 GitLab，剩下的 200MB 几乎是让系统处于“窒息”边缘。GitLab 默认是为企业级服务器设计的，会开启大量并发进程，对于你的个人环境来说确实太重了。

既然你已经配置了 **zram**，我们现在可以从 **GitLab 自身的配置文件**入手，进行“手术级”的瘦身。请编辑配置文件： 

```bash
sudo nano /etc/gitlab/gitlab.rb
```

找到并修改以下参数（如果前面有 `#` 请去掉）：

### 1. 限制 Puma 进程数（最有效的优化）

Puma 是 GitLab 的主 Web 服务器，默认会根据 CPU 核心数开启很多进程。

Ruby

```
# 限制最小和最大进程数，对于个人使用，2个足够了
puma['worker_processes'] = 2
# 限制每个进程占用的内存，超过则重启
puma['per_worker_max_memory_mb'] = 512 
```

### 2. 减少 Sidekiq 并发

Sidekiq 处理后台任务（如邮件、任务队列），默认占用较多资源。

Ruby

```
sidekiq['max_concurrency'] = 10
```

### 3. 关闭不必要的监控组件

如果你不需要查看复杂的服务器运行图表，可以关掉这些“吞金兽”：

Ruby

```
# 关闭 Prometheus 监控
prometheus_monitoring['enable'] = false

# 关闭 Grafana 图表
grafana['enable'] = false

# 关闭 Alertmanager
alertmanager['enable'] = false

# 关闭 Node Exporter
node_exporter['enable'] = false
```

### 4. 优化数据库 (PostgreSQL) 内存占用

Ruby

```
postgresql['shared_buffers'] = "256MB"
# 减少最大连接数
postgresql['max_connections'] = 50
```

------

### 5. 应用配置更改

保存文件后（Ctrl+O, Enter, Ctrl+X），**这是最关键的一步**： 由于 8GB 内存此时非常脆弱，直接运行 `reconfigure` 可能会导致系统死机。建议先停掉 GitLab，再应用配置，最后启动：

Bash

```
# 停止当前运行的 GitLab
sudo gitlab-ctl stop

# 应用新配置（此时内存压力最小）
sudo gitlab-ctl reconfigure

# 重新启动
sudo gitlab-ctl start
```

### 6. 进阶策略：调整 Linux 内核的 Swap 倾向

既然你设置了 4GB 的 zram，为了让这 200MB 的空闲内存不再是负担，你应该让内核更积极地使用 zram：

Bash

```
# 临时生效
sudo sysctl vm.swappiness=100

# 永久生效：编辑 /etc/sysctl.conf，添加或修改下面这行
vm.swappiness=100
```

### 优化后的预期结果：

完成这些操作后，再次运行 `free -m`，你的空闲内存（Available）应该能回升到 **1GB - 2GB** 左右。

**一个小提醒：** 由于你还有接送孩子和处理其他电脑的日常，如果这台 GitLab 服务器不需要 24 小时待命，你甚至可以写一个简单的脚本，在不用的时候 `sudo gitlab-ctl stop` 释放全部内存，要用的时候再开启。对于 8GB 内存的机器，这种“按需分配”也是一种折中方案。