





<img src="https://raw.githubusercontent.com/Arche-kinge/linux-learning-note/refs/heads/main/linux/OIP.webp">

Linux 的“版本和分支”容易混，
因为 **Linux 只是一个内核**，
而平时说的“Linux 系统”通常指 **发行版**。所以要先分清三层：

1. **Linux 内核版本**
2. **发行版及其版本**
3. **发行版家族/系列、发布模式**

---

## 1. Linux 内核版本

Linux 内核由 Linus Torvalds 和社区维护，官网 kernel.org。

版本号格式：

```text
主版本.次版本.修订号
6.8.0
```

- **主版本**：大功能变化，如 5.x → 6.x
- **次版本**：新功能、驱动、改进
- **修订号**：小修复、安全补丁

常见分支：

- **主线 mainline**：最新开发版，变化快
- **稳定 stable**：主线发布后维护的稳定版
- **长期支持 LTS**：维护几年，服务器常用
  - 如 6.6、6.1、5.15、5.10、5.4、4.19 等
- **发行版内核**：发行版自己打补丁、配置的内核，不一定等于官方版本

查看内核版本：

```bash
uname -r
uname -a
cat /proc/version
```

---

## 2. 发行版：真正的“Linux 系统”

发行版 = Linux 内核 + GNU 工具 + 包管理器 + 软件 + 安装程序 + 配置。

常见发行版版本例子：

- Ubuntu 24.04 LTS
- Debian 12
- Fedora 40
- RHEL 9
- openSUSE Leap 15.6
- Arch Linux（滚动更新，没有固定版本号）

发行版版本和内核版本是**独立**的。  
比如 Ubuntu 24.04 可能用内核 6.8，Debian 12 可能用内核 6.1。

查看发行版：

```bash
cat /etc/os-release
hostnamectl
lsb_release -a
```

---

## 3. 发行版家族/系列

按包管理、上游来源、设计理念分。

### Debian 系

- 代表：Debian、Ubuntu、Linux Mint、Pop!_OS、Kali、Raspberry Pi OS
- 包管理：`.deb`、`apt`、`dpkg`
- 特点：稳定、软件多、社区大

### Red Hat 系

- 代表：RHEL、Fedora、CentOS、CentOS Stream、Rocky Linux、AlmaLinux、Oracle Linux
- 包管理：`.rpm`、`dnf`、`yum`
- 特点：企业服务器常用，RHEL 是商业支持

### SUSE 系

- 代表：openSUSE Leap、openSUSE Tumbleweed、SUSE Linux Enterprise
- 包管理：`zypper`、`.rpm`
- 特点：欧洲企业常用，YaST 配置工具强

### Arch 系

- 代表：Arch Linux、Manjaro、EndeavourOS、Garuda
- 包管理：`pacman`、AUR
- 特点：滚动更新、轻量、文档好、适合折腾

### 独立发行版

- Gentoo：源码编译，高度定制
- Alpine：极小，容器常用，`apk`
- Void Linux：独立，`xbps`
- NixOS：声明式配置
- Slackware：古老、保守
- Android：基于 Linux 内核，但不是传统 GNU/Linux 发行版

---

## 4. 发布模式：固定版 vs 滚动版

### 固定发布

有明确版本号，如 Ubuntu 24.04、Fedora 40。  
每隔一段时间发布新版本，稳定，适合生产。

- Ubuntu：每 6 个月一版，每 2 年一个 LTS
- Fedora：每 6 个月一版
- Debian：约 2 年一版

### 滚动发布

没有固定版本，软件持续更新。

- Arch Linux
- openSUSE Tumbleweed
- Gentoo
- Manjaro（稍延迟）

### LTS 长期支持

- Ubuntu LTS：5 年支持
- RHEL：10 年
- Debian stable：约 5 年
- openSUSE Leap：较长支持

---

## 5. 常见发行版速查表

| 发行版              | 家族          | 包管理  | 发布模式      | 适合               |
| ------------------- | ------------- | ------- | ------------- | ------------------ |
| Debian              | Debian        | apt     | 固定          | 服务器、稳定       |
| Ubuntu              | Debian        | apt     | 固定 + LTS    | 桌面、服务器、新手 |
| Linux Mint          | Debian/Ubuntu | apt     | 固定 + LTS    | 桌面新手           |
| Fedora              | Red Hat       | dnf     | 固定          | 新技术、开发       |
| RHEL                | Red Hat       | dnf     | 固定 + 长支持 | 企业服务器         |
| Rocky / Alma        | Red Hat       | dnf     | 固定          | 替代 CentOS        |
| CentOS Stream       | Red Hat       | dnf     | 滚动          | RHEL 上游测试      |
| openSUSE Leap       | SUSE          | zypper  | 固定          | 服务器、桌面       |
| openSUSE Tumbleweed | SUSE          | zypper  | 滚动          | 追新               |
| Arch Linux          | Arch          | pacman  | 滚动          | 折腾、定制         |
| Manjaro             | Arch          | pacman  | 滚动          | Arch 新手          |
| Alpine              | 独立          | apk     | 固定          | 容器、轻量         |
| Gentoo              | 独立          | portage | 滚动          | 源码定制           |
| Kali                | Debian        | apt     | 滚动/固定     | 安全测试           |

---

## 6. 容易混淆的概念

- **Linux 内核 ≠ Linux 发行版**
- **发行版版本 ≠ 内核版本**
- **桌面环境 ≠ 发行版**：GNOME、KDE、XFCE 只是桌面
- **体系结构**：x86_64、ARM64、RISC-V 等
- **GNU/Linux**：强调 GNU 工具 + Linux 内核
- **Android**：用 Linux 内核，但不是传统发行版

---

## 7. 怎么选

- 新手桌面：Ubuntu LTS、Linux Mint、Pop!_OS
- 服务器稳定：Debian stable、Ubuntu LTS、RHEL、Rocky、Alma
- 追新开发：Fedora、Arch、openSUSE Tumbleweed
- 企业商业支持：RHEL、SUSE Linux Enterprise、Ubuntu Pro
- 容器轻量：Alpine
- 安全测试：Kali、Parrot
- 爱折腾：Arch、Gentoo、NixOS

---

## 8. 一句话总结

**Linux 内核有主版本、稳定版、LTS 分支；发行版有 Debian 系、Red Hat 系、SUSE 系、Arch 系等家族；发布模式分固定版和滚动版；版本号各自独立。**  
查看当前系统：

```bash
uname -r          # 内核版本
cat /etc/os-release   # 发行版版本
hostnamectl       # 系统概览
```