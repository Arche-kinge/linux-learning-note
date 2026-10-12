看谁在跑、占多少资源、怎么停。

bash

```bash
ps aux                   # 所有进程
ps -ef                   # 另一格式
top                      # 动态查看
htop                     # 更友好，需安装
pgrep nginx              # 按名字找 PID
kill PID                 # 发信号
kill -9 PID              # 强制杀
pkill nginx              # 按名字杀
nice -n 10 命令          # 调整优先级启动
renice -n 5 -p PID       # 调整已运行进程优先级
jobs                     # 当前 shell 后台任务
fg %1                    # 调回前台
bg %1                    # 放后台
```



ctrl + z组合键			# 暂停工作

&

fg

jobs

---

