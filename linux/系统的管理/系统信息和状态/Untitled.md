先知道“这台机器是什么情况”。

bash

```
uname -a                 # 内核、架构、主机名
hostnamectl              # 主机名、系统版本、内核
cat /etc/os-release      # 发行版信息
uptime                   # 运行时间、负载
free -h                  # 内存使用
df -h                    # 磁盘空间
lsblk                    # 块设备
lscpu                    # CPU 信息
lspci                    # PCI 设备
lsusb                    # USB 设备
dmesg                    # 内核日志
```