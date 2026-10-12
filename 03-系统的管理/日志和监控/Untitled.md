bash

```
journalctl -xe           # 系统日志
journalctl -u sshd       # 某服务日志
journalctl --since "1 hour ago"
tail -f /var/log/syslog
tail -f /var/log/messages
dmesg -T                 # 带时间的内核日志
```



性能监控：

bash

```
vmstat 1
iostat -x 1
sar -u 1 5
mpstat -P ALL 1
pidstat 1
iotop
nethogs
iftop
```