**SSH 客户端**是用来通过 SSH 协议远程登录、管理服务器/网络设备的软件。常见分为**命令行**和**图形化**两类。

## 常见 SSH 客户端

**命令行**
- **OpenSSH**：Linux/macOS 自带，Windows 10/11 也可安装。最常用。
- **Windows Terminal + OpenSSH**：Windows 上体验较好。
- **Termux**：Android 上的终端，可装 OpenSSH。

**图形化**
- **PuTTY**：Windows 经典、轻量。
- **MobaXterm**：Windows，多标签、SFTP、X11 转发。
- **Xshell**：Windows，功能强，有免费家庭/学校版。
- **FinalShell**：国产，集成 SFTP、监控。
- **Termius**：跨平台，界面现代，支持同步。
- **SecureCRT**：商业，企业常用。
- **iTerm2**：macOS 常用终端。
- **VS Code Remote-SSH**：适合开发，直接编辑远程文件。

## 基本用法

```bash
# 默认端口 22
ssh user@host

# 指定端口
ssh -p 2222 user@host

# 使用私钥
ssh -i ~/.ssh/id_rsa user@host

# 生成密钥
ssh-keygen -t ed25519

# 上传公钥到服务器
ssh-copy-id user@host
```

## 常用配置文件 `~/.ssh/config`

```sshconfig
Host myserver
    HostName 192.168.1.100
    User root
    Port 2222
    IdentityFile ~/.ssh/id_ed25519
```

之后直接：

```bash
ssh myserver
```

## 常用功能
- **端口转发**：`-L` 本地转发、`-R` 远程转发、`-D` 动态代理
- **免密登录**：公钥认证
- **文件传输**：`scp`、`sftp`、`rsync`
- **保持连接**：`ServerAliveInterval`
- **调试**：`ssh -v user@host`

