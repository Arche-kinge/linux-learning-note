不同发行版命令不同：

| 发行版        | 安装             | 更新                        | 搜索            | 删除            |
| :------------ | :--------------- | :-------------------------- | :-------------- | :-------------- |
| Debian/Ubuntu | `apt install`    | `apt update && apt upgrade` | `apt search`    | `apt remove`    |
| Fedora/RHEL   | `dnf install`    | `dnf upgrade`               | `dnf search`    | `dnf remove`    |
| Arch          | `pacman -S`      | `pacman -Syu`               | `pacman -Ss`    | `pacman -R`     |
| openSUSE      | `zypper install` | `zypper update`             | `zypper search` | `zypper remove` |