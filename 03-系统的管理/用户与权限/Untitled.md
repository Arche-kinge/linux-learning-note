系统管理最基础的部分。



<iframe frameborder="no" border="0" marginwidth="0" marginheight="0" width=298 height=52 src="//music.163.com/outchain/player?type=2&id=2634055151&auto=1&height=32"></iframe>

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



----

## 1.用户

user

> add   		#addite 添加
>
> mod		#modify 修改
>
> del			#delete 删除



## 2.群组

group

> add   		#addite 添加
>
> mod		#modify 修改
>
> del			#delete 删除

---

## 3.密码

passwd 

