来，一份 **Linux 磁盘管理速查 + 命令选项详细注释**。  
磁盘管理核心链路：**看磁盘 → 分区 → 格式化 → 挂载 → 检查修复 → 扩容/缩容 → LVM/RAID/Swap/配额 → 监控**。

> 危险提醒：`fdisk`、`parted`、`mkfs`、`dd`、`lvreduce`、`fsck` 等操作可能清空数据。生产环境先备份，先确认设备名，别把 `/dev/sda` 和 `/dev/sdb` 搞反。

---

## 1. 查看磁盘和分区

### `lsblk`
列出块设备，最常用。
```bash
lsblk
lsblk -f
lsblk -a -o NAME,SIZE,TYPE,FSTYPE,MOUNTPOINT,UUID
```

| 选项 | 含义                             |
| ---- | -------------------------------- |
| `-a` | 显示所有设备，包括空设备         |
| `-f` | 显示文件系统类型、UUID、挂载点   |
| `-l` | 树形改为列表                     |
| `-p` | 显示设备完整路径，如 `/dev/sda1` |
| `-o` | 自定义输出列                     |
| `-t` | 显示拓扑信息                     |
| `-d` | 只显示磁盘本身，不显示分区       |
| `-n` | 不显示标题                       |
| `-J` | JSON 输出                        |
| `-P` | key=value 输出                   |
| `-r` | 原始输出                         |
| `-S` | 显示 SCSI 设备信息               |
| `-m` | 显示权限和所有者                 |
| `-e` | 排除指定设备                     |

### `blkid`
查看块设备 UUID、LABEL、文件系统类型。
```bash
blkid
blkid /dev/sda1
blkid -s UUID -o value /dev/sda1
blkid -L DATA
blkid -U 1234-5678
```

| 选项 | 含义                               |
| ---- | ---------------------------------- |
| `-s` | 指定显示字段，如 UUID、LABEL、TYPE |
| `-o` | 输出格式，`value` 只显示值         |
| `-t` | 按条件过滤，如 `TYPE=ext4`         |
| `-L` | 按 LABEL 查找设备                  |
| `-U` | 按 UUID 查找设备                   |
| `-p` | 探测设备                           |
| `-i` | 显示 I/O 限制                      |
| `-g` | 垃圾回收缓存                       |

### `fdisk -l`
查看磁盘分区表。
```bash
fdisk -l
fdisk -l /dev/sda
```

| 选项 | 含义             |
| ---- | ---------------- |
| `-l` | 列出分区表       |
| `-u` | 以扇区为单位显示 |
| `-s` | 显示指定分区大小 |
| `-b` | 指定扇区大小     |
| `-C` | 指定柱面数       |
| `-H` | 指定磁头数       |
| `-S` | 指定每磁道扇区数 |

### `parted -l`
查看分区，支持 GPT。
```bash
parted -l
parted /dev/sda print
```

| 选项 | 含义                            |
| ---- | ------------------------------- |
| `-l` | 列出所有磁盘分区                |
| `-s` | 脚本模式，不交互                |
| `-a` | 对齐方式，`optimal` / `minimal` |
| `-m` | 机器可读输出                    |

### `df`
查看文件系统使用情况。
```bash
df -h
df -hT
df -i
df -h --total
```

| 选项       | 含义                |
| ---------- | ------------------- |
| `-h`       | 人类可读，如 G、M   |
| `-H`       | 以 1000 为单位      |
| `-T`       | 显示文件系统类型    |
| `-i`       | 显示 inode 使用情况 |
| `-a`       | 显示所有文件系统    |
| `-x`       | 排除指定类型        |
| `-t`       | 只显示指定类型      |
| `--total`  | 显示汇总            |
| `-P`       | POSIX 格式          |
| `-l`       | 只显示本地文件系统  |
| `--output` | 自定义输出字段      |

### `du`
查看目录占用。
```bash
du -sh /var
du -h --max-depth=1 /var
du -ah /home | sort -h
```

| 选项                 | 含义                       |
| -------------------- | -------------------------- |
| `-s`                 | 只显示总计                 |
| `-h`                 | 人类可读                   |
| `-a`                 | 显示所有文件               |
| `-d` / `--max-depth` | 指定深度                   |
| `-x`                 | 不跨文件系统               |
| `--exclude`          | 排除匹配文件               |
| `-c`                 | 显示总计                   |
| `-L`                 | 跟随符号链接               |
| `-P`                 | 不跟随符号链接             |
| `--apparent-size`    | 显示表面大小，而非磁盘占用 |

### `findmnt`
查看挂载关系。
```bash
findmnt
findmnt /home
findmnt -t ext4
findmnt -o TARGET,SOURCE,FSTYPE,OPTIONS
```

| 选项 | 含义               |
| ---- | ------------------ |
| `-l` | 列表输出           |
| `-t` | 按文件系统类型过滤 |
| `-o` | 自定义输出列       |
| `-n` | 不显示标题         |
| `-r` | 原始模式           |
| `-J` | JSON 输出          |
| `-s` | 只显示源设备       |
| `-M` | 按挂载点查找       |
| `-T` | 按设备查找         |

---

## 2. 分区管理

### `fdisk`
MBR/GPT 分区，交互式。
```bash
fdisk /dev/sdb
```

交互命令：

| 命令 | 含义            |
| ---- | --------------- |
| `m`  | 帮助            |
| `n`  | 新建分区        |
| `p`  | 打印分区表      |
| `d`  | 删除分区        |
| `t`  | 修改分区类型    |
| `l`  | 列出分区类型    |
| `a`  | 设置启动标志    |
| `w`  | 写入并退出      |
| `q`  | 不保存退出      |
| `g`  | 创建 GPT 分区表 |
| `o`  | 创建 MBR 分区表 |
| `v`  | 校验分区表      |
| `i`  | 显示分区信息    |

### `parted`
支持 GPT，可非交互。
```bash
parted /dev/sdb mklabel gpt
parted /dev/sdb mkpart primary ext4 1MiB 10GiB
parted /dev/sdb print
parted /dev/sdb rm 1
parted /dev/sdb resizepart 1 20GiB
parted /dev/sdb set 1 boot on
```

| 子命令       | 含义                         |
| ------------ | ---------------------------- |
| `mklabel`    | 创建分区表，`gpt` 或 `msdos` |
| `mkpart`     | 创建分区                     |
| `print`      | 打印分区表                   |
| `rm`         | 删除分区                     |
| `resizepart` | 调整分区大小                 |
| `set`        | 设置标志，如 boot、lvm、raid |
| `unit`       | 设置单位                     |
| `align`      | 设置对齐                     |
| `quit`       | 退出                         |

选项：

| 选项         | 含义         |
| ------------ | ------------ |
| `-l`         | 列出所有磁盘 |
| `-s`         | 脚本模式     |
| `-a optimal` | 最佳对齐     |
| `-m`         | 机器可读     |

### `gdisk`
GPT 专用，交互类似 `fdisk`。
```bash
gdisk /dev/sdb
```

| 命令 | 含义       |
| ---- | ---------- |
| `n`  | 新建分区   |
| `d`  | 删除分区   |
| `p`  | 打印分区表 |
| `t`  | 改类型     |
| `w`  | 写入       |
| `q`  | 退出       |
| `?`  | 帮助       |

---

## 3. 创建文件系统

### `mkfs.ext4`
```bash
mkfs.ext4 /dev/sdb1
mkfs.ext4 -L DATA -m 1 -b 4096 /dev/sdb1
```

| 选项 | 含义                         |
| ---- | ---------------------------- |
| `-L` | 设置卷标 LABEL               |
| `-m` | 保留给 root 的百分比，默认 5 |
| `-b` | 块大小，如 1024、2048、4096  |
| `-I` | inode 大小                   |
| `-O` | 启用/禁用特性                |
| `-E` | 扩展选项                     |
| `-F` | 强制创建                     |
| `-c` | 创建前检查坏块               |
| `-n` | 不真正创建，只显示           |
| `-q` | 安静                         |
| `-v` | 详细                         |

### `mkfs.xfs`
```bash
mkfs.xfs /dev/sdb1
mkfs.xfs -f -L DATA /dev/sdb1
```

| 选项 | 含义                   |
| ---- | ---------------------- |
| `-f` | 强制覆盖已有文件系统   |
| `-L` | 卷标                   |
| `-b` | 块大小                 |
| `-d` | 数据区参数             |
| `-l` | 日志区参数             |
| `-n` | 命名区参数             |
| `-m` | 元数据参数，如 `crc=0` |
| `-q` | 安静                   |
| `-V` | 版本                   |

### `mkfs.btrfs`
```bash
mkfs.btrfs /dev/sdb1
mkfs.btrfs -L DATA -d raid1 -m raid1 /dev/sdb1 /dev/sdc1
```

| 选项 | 含义             |
| ---- | ---------------- |
| `-L` | 卷标             |
| `-d` | 数据 RAID 模式   |
| `-m` | 元数据 RAID 模式 |
| `-f` | 强制             |
| `-K` | 不丢弃           |
| `-O` | 特性             |

### `mkfs.vfat`
```bash
mkfs.vfat -F 32 -n USB /dev/sdb1
```

| 选项 | 含义               |
| ---- | ------------------ |
| `-F` | FAT 位数，12/16/32 |
| `-n` | 卷标               |
| `-I` | 强制整个磁盘       |
| `-v` | 详细               |

### `mkswap`
```bash
mkswap /dev/sdb2
mkswap -L SWAP /dev/sdb2
```

| 选项 | 含义     |
| ---- | -------- |
| `-L` | 卷标     |
| `-p` | 页大小   |
| `-U` | UUID     |
| `-f` | 强制     |
| `-c` | 检查坏块 |

---

## 4. 挂载和卸载

### `mount`
```bash
mount /dev/sdb1 /mnt/data
mount -t ext4 -o rw,noatime /dev/sdb1 /mnt/data
mount -a
mount -o remount,rw /
mount --bind /data /mnt/data
mount -L DATA /mnt/data
mount -U UUID /mnt/data
```

| 选项             | 含义                               |
| ---------------- | ---------------------------------- |
| `-t`             | 文件系统类型                       |
| `-o`             | 挂载选项                           |
| `-a`             | 挂载 `/etc/fstab` 中所有 `auto` 项 |
| `-r`             | 只读                               |
| `-w`             | 读写                               |
| `-L`             | 按卷标挂载                         |
| `-U`             | 按 UUID 挂载                       |
| `-n`             | 不写入 mtab                        |
| `-v`             | 详细                               |
| `-B` / `--bind`  | 绑定挂载                           |
| `-M` / `--move`  | 移动挂载点                         |
| `-R` / `--rbind` | 递归绑定                           |
| `--make-private` | 设置挂载传播                       |

常用 `-o` 子选项：

| 子选项                      | 含义                                           |
| --------------------------- | ---------------------------------------------- |
| `defaults`                  | 默认：rw、suid、dev、exec、auto、nouser、async |
| `rw` / `ro`                 | 读写 / 只读                                    |
| `noatime`                   | 不更新访问时间                                 |
| `relatime`                  | 相对访问时间，默认常用                         |
| `nodiratime`                | 不更新目录访问时间                             |
| `noexec`                    | 禁止执行                                       |
| `nosuid`                    | 忽略 SUID/SGID                                 |
| `nodev`                     | 不解释设备文件                                 |
| `auto` / `noauto`           | 是否 `mount -a` 自动挂载                       |
| `user` / `users`            | 普通用户可挂载                                 |
| `nouser`                    | 仅 root 可挂载                                 |
| `sync` / `async`            | 同步 / 异步写入                                |
| `remount`                   | 重新挂载，如改 ro/rw                           |
| `bind`                      | 绑定挂载                                       |
| `loop`                      | 挂载镜像文件                                   |
| `offset`                    | 镜像偏移                                       |
| `sizelimit`                 | 限制大小                                       |
| `uid` / `gid`               | 指定所有者                                     |
| `umask` / `dmask` / `fmask` | 权限掩码                                       |
| `context`                   | SELinux 上下文                                 |
| `_netdev`                   | 网络设备，等网络就绪                           |
| `nofail`                    | 启动失败不阻塞                                 |
| `x-systemd.automount`       | systemd 自动挂载                               |

### `umount`
```bash
umount /mnt/data
umount -l /mnt/data
umount -f /mnt/data
umount -a -t nfs
```

| 选项 | 含义           |
| ---- | -------------- |
| `-l` | lazy，延迟卸载 |
| `-f` | 强制卸载       |
| `-a` | 卸载所有       |
| `-t` | 按类型卸载     |
| `-R` | 递归卸载       |
| `-n` | 不写 mtab      |
| `-v` | 详细           |

### `/etc/fstab`
字段：
```text
UUID=xxxx  /data  ext4  defaults,noatime  0  2
```

| 字段   | 含义                            |
| ------ | ------------------------------- |
| 设备   | UUID、LABEL、`/dev/sdb1`        |
| 挂载点 | `/data`                         |
| 类型   | `ext4`、`xfs`、`swap`           |
| 选项   | `defaults`、`noatime`、`nofail` |
| dump   | 一般 `0`                        |
| pass   | `0` 不检查，`1` 根，`2` 其他    |

推荐用 UUID：
```bash
blkid /dev/sdb1
```

改完测试：
```bash
mount -a
```

---

## 5. 检查与修复

### `fsck`
```bash
fsck /dev/sdb1
fsck -y /dev/sdb1
fsck -n /dev/sdb1
fsck -A
```

| 选项 | 含义              |
| ---- | ----------------- |
| `-A` | 检查 fstab 中所有 |
| `-a` | 自动修复          |
| `-y` | 全部回答 yes      |
| `-n` | 只检查不修复      |
| `-f` | 强制检查          |
| `-t` | 指定类型          |
| `-C` | 进度条            |
| `-V` | 详细              |
| `-p` | 安全自动修复      |
| `-r` | 交互修复          |

### `e2fsck`
ext2/3/4 专用。
```bash
e2fsck -f /dev/sdb1
e2fsck -p /dev/sdb1
e2fsck -y /dev/sdb1
```

| 选项 | 含义           |
| ---- | -------------- |
| `-p` | 自动修复安全项 |
| `-y` | 全部 yes       |
| `-n` | 只检查         |
| `-f` | 强制检查       |
| `-c` | 检查坏块       |
| `-b` | 使用备用超级块 |
| `-B` | 指定块大小     |
| `-D` | 优化目录       |
| `-E` | 扩展选项       |
| `-L` | 设置卷标       |
| `-z` | 撤销文件       |

### `xfs_repair`
```bash
xfs_repair -n /dev/sdb1
xfs_repair /dev/sdb1
xfs_repair -L /dev/sdb1
```

| 选项 | 含义                     |
| ---- | ------------------------ |
| `-n` | 只检查不修复             |
| `-L` | 清空日志，危险           |
| `-v` | 详细                     |
| `-d` | 危险，允许修复已挂载设备 |
| `-m` | 限制内存                 |

### `tune2fs`
调整 ext 文件系统参数。
```bash
tune2fs -l /dev/sdb1
tune2fs -m 1 /dev/sdb1
tune2fs -L DATA /dev/sdb1
tune2fs -c 30 /dev/sdb1
```

| 选项 | 含义                 |
| ---- | -------------------- |
| `-l` | 查看超级块信息       |
| `-m` | 保留给 root 的百分比 |
| `-L` | 设置卷标             |
| `-c` | 最大挂载次数后检查   |
| `-i` | 检查间隔             |
| `-U` | 设置 UUID            |
| `-O` | 启用/禁用特性        |
| `-e` | 错误行为             |

---

## 6. 扩容与缩容

### `resize2fs`
ext2/3/4 调整大小。
```bash
resize2fs /dev/sdb1
resize2fs /dev/sdb1 20G
resize2fs -p /dev/sdb1
```

| 选项 | 含义           |
| ---- | -------------- |
| `-p` | 显示进度       |
| `-f` | 强制           |
| `-d` | 调试           |
| `-M` | 缩到最小       |
| `-s` | 缩小到指定大小 |

缩容前必须卸载并 `e2fsck -f`。

### `xfs_growfs`
XFS 只能扩，不能缩。
```bash
xfs_growfs /data
xfs_growfs -d /data
```

| 选项 | 含义              |
| ---- | ----------------- |
| `-d` | 扩展到设备最大    |
| `-D` | 指定数据区大小    |
| `-L` | 指定日志区大小    |
| `-m` | 最大 inode 百分比 |
| `-n` | 不改变            |
| `-R` | 实时设备          |

### `btrfs filesystem resize`
```bash
btrfs filesystem resize max /data
btrfs filesystem resize +10G /data
```

| 写法   | 含义     |
| ------ | -------- |
| `max`  | 扩到最大 |
| `+10G` | 增加 10G |
| `-10G` | 减少 10G |

### 分区扩容：`parted resizepart`
```bash
parted /dev/sdb resizepart 1 100%
```

顺序通常是：
1. 扩分区
2. 扩 PV
3. 扩 LV
4. 扩文件系统

---

## 7. LVM 逻辑卷管理

LVM 三层：**PV 物理卷 → VG 卷组 → LV 逻辑卷**。

### `pvcreate`
```bash
pvcreate /dev/sdb1
pvcreate -f /dev/sdb1
```

| 选项  | 含义          |
| ----- | ------------- |
| `-f`  | 强制          |
| `-y`  | 全部 yes      |
| `-u`  | 指定 UUID     |
| `-Z`  | 清零前 4 扇区 |
| `-M2` | 元数据格式    |

### `pvdisplay` / `pvs`
```bash
pvs
pvdisplay
pvdisplay /dev/sdb1
```

| 选项 | 含义     |
| ---- | -------- |
| `-v` | 详细     |
| `-m` | 显示映射 |
| `-c` | 列格式   |

### `vgcreate`
```bash
vgcreate vg_data /dev/sdb1 /dev/sdc1
vgcreate -s 16M vg_data /dev/sdb1
```

| 选项 | 含义       |
| ---- | ---------- |
| `-s` | PE 大小    |
| `-l` | 最大 LV 数 |
| `-p` | 最大 PV 数 |

### `vgextend` / `vgreduce`
```bash
vgextend vg_data /dev/sdd1
vgreduce vg_data /dev/sdd1
```

| 选项 | 含义 |
| ---- | ---- |
| `-f` | 强制 |
| `-a` | 自动 |
| `-y` | yes  |

### `lvcreate`
```bash
lvcreate -L 10G -n lv_data vg_data
lvcreate -l 100%FREE -n lv_data vg_data
lvcreate -s -L 1G -n snap /dev/vg_data/lv_data
```

| 选项 | 含义               |
| ---- | ------------------ |
| `-L` | 指定大小，如 10G   |
| `-l` | 指定 PE 数或百分比 |
| `-n` | 逻辑卷名           |
| `-s` | 创建快照           |
| `-i` | 条带数             |
| `-I` | 条带大小           |
| `-m` | 镜像数             |
| `-W` | 写多数             |
| `-y` | 全部 yes           |
| `-Z` | 清零               |

### `lvextend`
```bash
lvextend -L +10G /dev/vg_data/lv_data
lvextend -l +100%FREE /dev/vg_data/lv_data
lvextend -L +10G -r /dev/vg_data/lv_data
```

| 选项 | 含义             |
| ---- | ---------------- |
| `-L` | 增加/设置大小    |
| `-l` | 按 PE 或百分比   |
| `-r` | 同时调整文件系统 |
| `-f` | 强制             |
| `-n` | 不调整           |

### `lvreduce`
```bash
lvreduce -L -10G /dev/vg_data/lv_data
lvreduce -L 20G -r /dev/vg_data/lv_data
```

| 选项 | 含义             |
| ---- | ---------------- |
| `-L` | 减少/设置大小    |
| `-l` | 按 PE 或百分比   |
| `-r` | 同时调整文件系统 |
| `-f` | 强制             |

缩容危险，先缩文件系统，再缩 LV。

### `lvremove` / `vgremove` / `pvremove`
```bash
lvremove /dev/vg_data/lv_data
vgremove vg_data
pvremove /dev/sdb1
```

| 选项 | 含义 |
| ---- | ---- |
| `-f` | 强制 |
| `-y` | 确认 |

### `pvresize`
```bash
pvresize /dev/sdb1
```

| 选项                      | 含义     |
| ------------------------- | -------- |
| `-v`                      | 详细     |
| `-y`                      | yes      |
| `--setphysicalvolumesize` | 设置大小 |

---

## 8. RAID：`mdadm`

```bash
mdadm --create /dev/md0 --level=1 --raid-devices=2 /dev/sdb1 /dev/sdc1
mdadm --detail /dev/md0
mdadm --scan --detail
mdadm --examine /dev/sdb1
mdadm --add /dev/md0 /dev/sdd1
mdadm --remove /dev/md0 /dev/sdd1
mdadm --fail /dev/md0 /dev/sdb1
mdadm --grow /dev/md0 --raid-devices=3
mdadm --assemble /dev/md0 /dev/sdb1 /dev/sdc1
mdadm --stop /dev/md0
```

| 选项              | 含义                   |
| ----------------- | ---------------------- |
| `--create`        | 创建阵列               |
| `--level`         | RAID 级别，0/1/5/6/10  |
| `--raid-devices`  | 活动设备数             |
| `--spare-devices` | 热备数                 |
| `--chunk`         | 条带大小               |
| `--metadata`      | 元数据版本             |
| `--assume-clean`  | 假设干净，跳过初始同步 |
| `--detail`        | 查看详情               |
| `--scan`          | 扫描配置               |
| `--examine`       | 检查设备元数据         |
| `--add`           | 添加设备               |
| `--remove`        | 移除设备               |
| `--fail`          | 标记故障               |
| `--grow`          | 扩容/改级别            |
| `--assemble`      | 组装阵列               |
| `--stop`          | 停止阵列               |

查看：
```bash
cat /proc/mdstat
```

---

## 9. Swap 交换分区

```bash
mkswap /dev/sdb2
swapon /dev/sdb2
swapon -a
swapon -s
swapoff /dev/sdb2
swapoff -a
free -h
```

| 命令/选项    | 含义                   |
| ------------ | ---------------------- |
| `mkswap`     | 创建 swap              |
| `swapon`     | 启用 swap              |
| `swapon -a`  | 启用 fstab 中所有 swap |
| `swapon -s`  | 查看 swap              |
| `swapon -p`  | 设置优先级             |
| `swapoff`    | 关闭 swap              |
| `swapoff -a` | 关闭所有 swap          |
| `free -h`    | 查看内存和 swap        |

`/etc/fstab`：
```text
UUID=xxxx  none  swap  sw  0  0
```

---

## 10. 磁盘配额

```bash
quotaon -avug
quotaoff -avug
quotacheck -avug
edquota -u alice
repquota -avug
quota -u alice
```

| 命令/选项    | 含义         |
| ------------ | ------------ |
| `quotaon`    | 启用配额     |
| `-a`         | 所有         |
| `-v`         | 详细         |
| `-u`         | 用户         |
| `-g`         | 组           |
| `quotaoff`   | 关闭配额     |
| `quotacheck` | 检查配额     |
| `-c`         | 创建配额文件 |
| `-m`         | 不挂载       |
| `edquota`    | 编辑配额     |
| `-p`         | 复制配额     |
| `-t`         | 宽限时间     |
| `repquota`   | 报告配额     |
| `-s`         | 人类可读     |

---

## 11. `dd` 磁盘备份与写入

```bash
dd if=/dev/sda of=/backup/mbr.img bs=512 count=1
dd if=/dev/zero of=/dev/sdb bs=4M status=progress
dd if=image.iso of=/dev/sdc bs=4M status=progress conv=fsync
```

| 选项              | 含义       |
| ----------------- | ---------- |
| `if`              | 输入文件   |
| `of`              | 输出文件   |
| `bs`              | 块大小     |
| `count`           | 块数量     |
| `skip`            | 跳过输入块 |
| `seek`            | 跳过输出块 |
| `status=progress` | 显示进度   |
| `conv=fsync`      | 完成后同步 |
| `conv=noerror`    | 出错继续   |
| `conv=sync`       | 填充块     |
| `conv=notrunc`    | 不截断     |
| `conv=swab`       | 交换字节   |
| `iflag=direct`    | 直接 I/O   |
| `oflag=direct`    | 直接 I/O   |

`dd` 非常危险，`of=` 写错设备会直接毁数据。

---

## 12. SMART 磁盘健康

```bash
smartctl -a /dev/sda
smartctl -i /dev/sda
smartctl -H /dev/sda
smartctl -t short /dev/sda
smartctl -t long /dev/sda
smartctl -l selftest /dev/sda
smartctl -s on /dev/sda
```

| 选项 | 含义                       |
| ---- | -------------------------- |
| `-a` | 显示全部信息               |
| `-i` | 设备信息                   |
| `-H` | 健康状态                   |
| `-A` | 属性表                     |
| `-t` | 自检，`short` / `long`     |
| `-l` | 日志，`selftest` / `error` |
| `-s` | 启用/禁用 SMART            |
| `-x` | 扩展信息                   |

后台服务：
```bash
systemctl status smartd
```

---

## 13. 性能与监控

### `iostat`
```bash
iostat -x 1
iostat -d -m 1
```

| 选项 | 含义     |
| ---- | -------- |
| `-x` | 扩展统计 |
| `-d` | 设备     |
| `-m` | MB 单位  |
| `-k` | KB 单位  |
| `-t` | 时间戳   |
| `-c` | CPU      |
| `-N` | 设备映射 |
| `-p` | 分区     |

### `iotop`
```bash
iotop -o
iotop -b -n 3
```

| 选项 | 含义         |
| ---- | ------------ |
| `-o` | 只显示有 I/O |
| `-b` | 批处理       |
| `-n` | 次数         |
| `-d` | 间隔         |
| `-p` | PID          |
| `-u` | 用户         |
| `-P` | 进程         |
| `-a` | 累计         |
| `-k` | KB           |

### `hdparm`
```bash
hdparm -tT /dev/sda
hdparm -I /dev/sda
```

| 选项 | 含义       |
| ---- | ---------- |
| `-t` | 缓存读性能 |
| `-T` | 磁盘读性能 |
| `-I` | 详细信息   |
| `-i` | 设备信息   |
| `-A` | 预读       |
| `-B` | 电源管理   |
| `-M` | 声音管理   |
| `-S` | 休眠       |
| `-W` | 写缓存     |
| `-c` | IDE 32 位  |
| `-d` | DMA        |
| `-r` | 只读       |

---

## 14. 常见任务速查

| 任务        | 命令                                       |
| ----------- | ------------------------------------------ |
| 看磁盘      | `lsblk -f`                                 |
| 看分区      | `fdisk -l` / `parted -l`                   |
| 看使用率    | `df -hT`                                   |
| 看目录占用  | `du -sh /path`                             |
| 看 UUID     | `blkid`                                    |
| 分区        | `fdisk /dev/sdb` / `parted /dev/sdb`       |
| 格式化 ext4 | `mkfs.ext4 /dev/sdb1`                      |
| 格式化 xfs  | `mkfs.xfs /dev/sdb1`                       |
| 挂载        | `mount /dev/sdb1 /mnt`                     |
| 卸载        | `umount /mnt`                              |
| 自动挂载    | 编辑 `/etc/fstab`                          |
| 检查 ext4   | `e2fsck -f /dev/sdb1`                      |
| 检查 xfs    | `xfs_repair -n /dev/sdb1`                  |
| 扩 ext4     | `resize2fs /dev/sdb1`                      |
| 扩 xfs      | `xfs_growfs /data`                         |
| LVM 创建    | `pvcreate` → `vgcreate` → `lvcreate`       |
| LVM 扩容    | `lvextend -L +10G -r /dev/vg/lv`           |
| RAID        | `mdadm --create ...`                       |
| Swap        | `mkswap` → `swapon`                        |
| 磁盘健康    | `smartctl -a /dev/sda`                     |
| 备份 MBR    | `dd if=/dev/sda of=mbr.img bs=512 count=1` |

---

## 15. 排错基本顺序

```bash
lsblk -f                 # 设备、文件系统、挂载点
blkid                    # UUID
df -hT                   # 空间使用
df -i                    # inode 是否满
du -sh * | sort -h       # 目录占用
findmnt                  # 挂载关系
dmesg | tail -50         # 内核报错
smartctl -a /dev/sda     # 磁盘健康
iostat -x 1              # I/O 瓶颈
```

---

一句话总结：  
**看盘用 `lsblk`、`df`、`du`；分区用 `fdisk`、`parted`；格式化用 `mkfs`；挂载用 `mount`、`fstab`；检查修复用 `fsck`、`xfs_repair`；扩容缩容用 `resize2fs`、`xfs_growfs`、`lvextend`；LVM 记 PV→VG→LV；RAID 用 `mdadm`；Swap 用 `mkswap`、`swapon`；健康监控用 `smartctl`、`iostat`。**