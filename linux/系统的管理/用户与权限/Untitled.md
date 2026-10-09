系统管理最基础的部分。

bash

```
useradd -m alice         # 创建用户并建家目录
passwd alice             # 设置密码
usermod -aG sudo alice   # 加入 sudo 组
userdel -r alice         # 删除用户和家目录
groupadd dev             # 创建组
id alice                 # 查看 UID/GID/组
who                      # 当前登录用户
w                        # 登录用户及在做什么
last                     # 登录历史
sudo 命令                # 以 root 执行
visudo                   # 安全编辑 sudo 配置
```



权限相关：

bash

```
chmod 755 file
chown alice:dev file
chgrp dev file
umask 022
getfacl file
setfacl -m u:alice:rw file
```



