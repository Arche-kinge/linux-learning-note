bash

```
lsblk                    # 查看磁盘和分区
fdisk -l                 # 列出分区
parted /dev/sdb          # 分区工具
mkfs.ext4 /dev/sdb1      # 格式化
mount /dev/sdb1 /mnt     # 挂载
umount /mnt              # 卸载
blkid                    # 查看 UUID
cat /etc/fstab           # 开机自动挂载配置
df -h                    # 空间使用
du -sh /var/log          # 目录占用
lsof +D /mnt             # 谁占用挂载点
```



LVM：

bash

```
pvcreate /dev/sdb1
vgcreate vg0 /dev/sdb1
lvcreate -L 10G -n lv0 vg0
mkfs.ext4 /dev/vg0/lv0
```