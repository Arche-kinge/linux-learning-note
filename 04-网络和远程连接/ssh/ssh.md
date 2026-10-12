## SSH 全称

**SSH = Secure Shell**，中文常叫“安全外壳协议”。  
标准全称是 **Secure Shell**，不是 Secure Socket Shell。当前主流版本是 **SSH-2**，默认端口 **22**。

SSH 用于在不安全网络上安全地：
- 远程登录服务器
- 执行命令
- 传输文件
- 做端口转发 / 隧道
- 通过跳板机访问内网机器

---

## 1. SSH 基本原理

SSH 是客户端—服务器模型：

- 客户端：你本地的 `ssh` 命令、PuTTY、Xshell、Termius 等
- 服务端：远程 Linux 上的 `sshd`
- 加密：协商出对称密钥加密通信
- 认证：支持密码认证、公钥认证
- 完整性：防止数据被篡改

第一次连接会提示远程主机指纹，确认后写入 `~/.ssh/known_hosts`。

---

## 2. 安装 SSH

### Linux 客户端
大多数 Linux 自带 OpenSSH 客户端：

```bash
ssh -V
```

没有就安装：

```bash
sudo apt install openssh-client
```

### Linux 服务端
Ubuntu / Debian：

```bash
sudo apt update
sudo apt install openssh-server
sudo systemctl enable --now ssh
sudo systemctl status ssh
```

CentOS / RHEL：

```bash
sudo dnf install openssh-server
sudo systemctl enable --now sshd
```

查看端口：

```bash
sudo ss -tlnp | grep :22
```

防火墙放行：

```bash
sudo ufw allow 22/tcp
```

### Windows
Windows 10/11 可在“设置 → 应用 → 可选功能”中安装：
- OpenSSH 客户端
- OpenSSH 服务器

然后 PowerShell 里直接：

```powershell
ssh user@host
```

---

## 3. 基本登录

```bash
ssh 用户名@IP或域名
```

例如：

```bash
ssh root@192.168.1.10
ssh ubuntu@example.com
```

指定端口：

```bash
ssh -p 2222 user@host
```

登录后退出：

```bash
exit
```

或按 `Ctrl + D`。

---

## 4. 公钥免密登录（推荐）

### 1）生成密钥

```bash
ssh-keygen -t ed25519 -C "your_email@example.com"
```

默认生成：

```text
~/.ssh/id_ed25519      私钥，绝不能给别人
~/.ssh/id_ed25519.pub  公钥，可以上传到服务器
```

### 2）上传公钥

有 `ssh-copy-id`：

```bash
ssh-copy-id user@host
```

没有就手动：

```bash
cat ~/.ssh/id_ed25519.pub | ssh user@host \
  "mkdir -p ~/.ssh && chmod 700 ~/.ssh && \
   cat >> ~/.ssh/authorized_keys && chmod 600 ~/.ssh/authorized_keys"
```

### 3）测试

```bash
ssh -i ~/.ssh/id_ed25519 user@host
```

### 4）权限要求

服务器上：

```bash
chmod 700 ~/.ssh
chmod 600 ~/.ssh/authorized_keys
```

本地私钥：

```bash
chmod 600 ~/.ssh/id_ed25519
```

权限不对，SSH 会拒绝使用密钥。

---

## 5. SSH 配置文件

编辑：

```bash
nano ~/.ssh/config
```

示例：

```sshconfig
Host myserver
    HostName 192.168.1.10
    User ubuntu
    Port 22
    IdentityFile ~/.ssh/id_ed25519
    ServerAliveInterval 60
```

以后直接：

```bash
ssh myserver
```

跳板机写法：

```sshconfig
Host target
    HostName 10.0.0.5
    User root
    ProxyJump jumpuser@jump.example.com
```

或者命令行：

```bash
ssh -J jumpuser@jump.example.com root@10.0.0.5
```

---

## 6. 文件传输

### scp

```bash
scp file.txt user@host:/home/user/
scp -r mydir user@host:/home/user/
scp user@host:/home/user/file.txt ./
```

### sftp

```bash
sftp user@host
```

常用命令：

```text
ls
cd
put file.txt
get file.txt
exit
```

### rsync

```bash
rsync -avz -e ssh ./mydir user@host:/home/user/
```

---

## 7. 端口转发 / SSH 隧道

### 本地转发 `-L`

```bash
ssh -L 8080:127.0.0.1:80 user@remote
```

含义：访问你本地的 `8080`，等于访问远程机器上的 `127.0.0.1:80`。

### 远程转发 `-R`

```bash
ssh -R 9090:127.0.0.1:3000 user@remote
```

含义：远程机器的 `9090` 转发到你本地的 `3000`。

### 动态代理 `-D`

```bash
ssh -D 1080 -N user@remote
```

浏览器设置 SOCKS5 代理：

```text
127.0.0.1:1080
```

### 后台运行

```bash
ssh -f -N -L 8080:127.0.0.1:80 user@remote
```

---

## 8. 服务端安全配置

配置文件：

```bash
sudo nano /etc/ssh/sshd_config
```

常见安全项：

```sshconfig
Port 22
PermitRootLogin prohibit-password
PubkeyAuthentication yes
PasswordAuthentication no
AllowUsers alice bob
```

改完检查：

```bash
sudo sshd -t
```

重启：

```bash
sudo systemctl restart ssh
```

或：

```bash
sudo systemctl restart sshd
```

> 注意：禁用密码登录前，一定确认公钥登录已经成功，否则可能把自己锁在外面。

其他建议：
- 使用 `ed25519` 密钥
- 私钥设置 passphrase
- 安装 `fail2ban`
- 防火墙只放行必要端口
- 定期更新 OpenSSH

---

## 9. 常见问题

### Connection refused
服务没启动、端口不对、防火墙拦截。

```bash
sudo systemctl status ssh
sudo ss -tlnp | grep ssh
```

### Permission denied (publickey)
检查：
- 公钥是否在 `~/.ssh/authorized_keys`
- 权限是否为 `700` / `600`
- 用户名是否正确
- SELinux 是否拦截：

```bash
restorecon -R ~/.ssh
```

### Host key verification failed
远程主机密钥变了，清理旧记录：

```bash
ssh-keygen -R host
```

### 调试

```bash
ssh -vvv user@host
```

---

## 10. 学习路线

1. `ssh user@host` 密码登录
2. `ssh-keygen` + `ssh-copy-id` 免密登录
3. 配置 `~/.ssh/config` 别名
4. `scp` / `sftp` 传文件
5. `-L` / `-R` / `-D` 端口转发
6. `ProxyJump` 跳板机
7. 服务端加固：禁密码、禁 root、fail2ban

掌握这些，日常服务器管理基本够用了。