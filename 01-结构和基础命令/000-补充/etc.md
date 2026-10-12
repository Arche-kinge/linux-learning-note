Linux 根目录 `/` 下的文件夹基本遵循 
**FHS（Filesystem Hierarchy Standard，文件系统层次标准）**。

它们各有分工，可以按“是否可编辑、是否主机特定、是否只读”来理解。



先给一个总览表：

| 目录             | 作用                   | 是否可编辑/可变            | 典型内容                   |
| ---------------- | ---------------------- | -------------------------- | -------------------------- |
| `/etc`           | 系统级配置文件         | 可编辑，主机特定           | passwd、fstab、sshd_config |
| `/bin`           | 基本用户命令           | 一般只读                   | ls、cp、mv、bash           |
| `/sbin`          | 系统管理命令           | 一般只读                   | fdisk、mkfs、reboot        |
| `/lib`、`/lib64` | 共享库和内核模块       | 一般只读                   | .so 库、modules            |
| `/usr`           | 用户级程序、库、文档   | 一般只读                   | bin、lib、share、local     |
| `/var`           | 经常变化的数据         | 可变、可写                 | log、cache、lib、spool     |
| `/home`          | 普通用户家目录         | 用户可写                   | /home/user                 |
| `/root`          | root 用户家目录        | root 可写                  | root 的配置和文件          |
| `/tmp`           | 临时文件               | 所有用户可写，可能重启清空 | 临时文件、socket           |
| `/opt`           | 第三方可选软件         | 一般只读                   | 独立安装的软件             |
| `/srv`           | 服务数据               | 可写                       | Web、FTP 等数据            |
| `/mnt`           | 临时挂载点             | 可写                       | 手动挂载的磁盘             |
| `/media`         | 自动挂载点             | 可写                       | U 盘、光盘                 |
| `/boot`          | 启动相关文件           | 一般只读                   | 内核、initrd、grub         |
| `/dev`           | 设备文件               | 虚拟文件系统               | sda、tty、null             |
| `/proc`          | 内核和进程信息         | 虚拟文件系统               | CPU、内存、进程信息        |
| `/sys`           | 内核设备信息           | 虚拟文件系统               | 设备、驱动、内核参数       |
| `/run`           | 运行时数据             | 虚拟文件系统，重启清空     | pid、socket、锁文件        |
| `/lost+found`    | 文件系统修复找回的文件 | 一般不用手动碰             | fsck 恢复的碎片            |

下面逐类解释。







## 1. `/bin`、`/sbin`、`/lib`

这三个是系统最基础的部分。

- `/bin`：基本用户命令，比如 `ls`、`cp`、`mv`、`cat`、`bash`。
- `/sbin`：系统管理命令，通常 root 使用，比如 `fdisk`、`mkfs`、`reboot`、`iptables`。
- `/lib`、`/lib64`：共享库和内核模块。程序运行依赖的 `.so` 文件大多在这里。

在现代 Linux 上，很多发行版做了 **usr merge**，你会看到：

```bash
/bin -> /usr/bin
/sbin -> /usr/sbin
/lib -> /usr/lib
/lib64 -> /usr/lib64
```

也就是这些目录实际指向 `/usr` 下的对应目录。

## 2. `/usr`

`/usr` 不是 “user” 的缩写，而是 **Unix System Resources**。它放的是用户级程序、库、文档等，通常是只读的、跨机器通用的。

常见子目录：

| 子目录         | 作用                                       |
| -------------- | ------------------------------------------ |
| `/usr/bin`     | 大多数用户命令                             |
| `/usr/sbin`    | 系统管理命令                               |
| `/usr/lib`     | 库文件                                     |
| `/usr/include` | C/C++ 头文件                               |
| `/usr/share`   | 共享只读数据，如 man、doc、icons、locale   |
| `/usr/local`   | 管理员手动安装的软件，通常不被包管理器管理 |
| `/usr/src`     | 内核源码等                                 |

你之前 `whereis ls` 看到的 `/usr/share/man/man1/ls.1.gz` 就在 `/usr/share` 下。

## 3. `/var`

`/var` 是 **variable**，存放经常变化的数据。和 `/usr` 的只读相对。

常见子目录：

| 子目录       | 作用                                     |
| ------------ | ---------------------------------------- |
| `/var/log`   | 系统和服务日志                           |
| `/var/cache` | 缓存数据，如 apt 缓存                    |
| `/var/lib`   | 程序运行需要的状态数据，如数据库、Docker |
| `/var/spool` | 队列数据，如打印、邮件                   |
| `/var/tmp`   | 临时文件，重启后通常保留                 |
| `/var/run`   | 现代系统通常链接到 `/run`                |
| `/var/www`   | 某些发行版的 Web 根目录                  |

## 4. `/home` 和 `/root`

- `/home`：普通用户的家目录，比如 `/home/alice`、`/home/bob`。用户自己可写。
- `/root`：root 用户的家目录，不是 `/home/root`。权限很严。

用户自己的配置文件通常在家目录的隐藏文件里，比如：

```bash
~/.bashrc
~/.vimrc
~/.ssh/
~/.config/
```

## 5. `/tmp`

临时文件目录。所有用户通常都可以写，但有以下特点：

- 权限特殊，常有 sticky bit，用户只能删自己的文件。
- 很多系统重启后会清空。
- 适合放临时文件，不适合长期保存。

## 6. `/opt`、`/srv`

- `/opt`：optional，第三方软件。很多商业软件、独立软件装在这里，比如 `/opt/google/chrome`。
- `/srv`：service，服务数据。比如 Web 服务的网页文件、FTP 数据。实际使用中不一定很规范。

## 7. `/mnt` 和 `/media`

- `/mnt`：手动挂载点。管理员临时挂载磁盘、NFS 等。
- `/media`：自动挂载点。插入 U 盘、光盘时，系统通常挂到 `/media/用户名/设备名`。

## 8. `/boot`

启动相关文件：

- 内核：`vmlinuz-*`
- initrd/initramfs：`initrd.img-*`
- GRUB 配置：`/boot/grub/`
- 有时还有 EFI 分区挂载点 `/boot/efi`

这个目录不要乱动，删了可能导致系统无法启动。

## 9. `/dev`、`/proc`、`/sys`、`/run`

这四个是虚拟文件系统，不占实际磁盘，反映内核和运行时状态。

- `/dev`：设备文件。`/dev/sda`、`/dev/null`、`/dev/tty`。
- `/proc`：进程和内核信息。`/proc/cpuinfo`、`/proc/meminfo`、`/proc/<pid>`。
- `/sys`：设备和驱动信息，sysfs。可以查看和调整内核参数。
- `/run`：运行时数据，重启清空。PID 文件、socket、锁文件常在这里。

## 10. `/lost+found`

每个文件系统根目录下可能有这个目录。`fsck` 修复文件系统时，找回的孤立文件会放到这里。平时不用管。

## 11. 和 `/etc` 的关系

可以这样记：

| 目录                    | 角色                   |
| ----------------------- | ---------------------- |
| `/etc`                  | 配置                   |
| `/usr`                  | 程序、库、共享只读数据 |
| `/var`                  | 经常变化的数据         |
| `/home`                 | 用户数据               |
| `/tmp`                  | 临时数据               |
| `/opt`                  | 第三方软件             |
| `/dev`、`/proc`、`/sys` | 内核和设备的虚拟视图   |

## 12. 查看帮助

系统里通常有：

```bash
man hier
```

它会解释文件系统层次结构。也可以：

```bash
ls -l /
```

看看根目录下有哪些目录和软链接。不同发行版可能略有差异，但大体遵循 FHS。

一句话总结：  
**`/etc` 管配置，`/usr` 放程序，`/var` 存变化，`/home` 归用户，`/tmp` 是临时，`/dev`、`/proc`、`/sys` 看内核。**