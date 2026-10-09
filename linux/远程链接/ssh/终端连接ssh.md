在本地电脑的**终端（Terminal）**里直接用 SSH 连接阿里云 ECS，是日常开发运维最常用的方式。它比网页版 Workbench 反应更快、更流畅。

以下是完整步骤和常见问题：

---

### 一、连接前的准备工作（必看）

在打开终端之前，请确保：
1. **拿到公网 IP**：在阿里云 ECS 控制台实例列表，复制实例的**公网 IP**。
2. **确认登录名**：
   - Linux 通常是 `root` 或 `ecs-user`（阿里云部分镜像默认是 `ecs-user`）。
   - Windows 通常是 `Administrator`。
3. **确认密码或密钥**：在控制台重置过密码，或者创建实例时下载了 `.pem` 密钥文件。
4. **检查安全组**：阿里云安全组必须放行 **22 端口**（Linux SSH）。如果没放行，终端会一直卡在 `Connecting...` 最后超时。

---

### 二、具体操作步骤

#### 场景 1：使用密码登录（最简单）

**1. 打开终端**
- **Windows**：按 `Win + R`，输入 `powershell` 或 `cmd`，回车。
- **macOS**：按 `Command + 空格`，搜索 `Terminal`（终端），回车。
- **Linux**：直接打开终端。

**2. 输入 SSH 命令**
```bash
ssh root@你的公网IP
```
例如：`ssh root@47.100.xx.xx`

**3. 首次连接提示**
会提示：
```text
The authenticity of host '47.100.xx.xx' can't be established.
Are you sure you want to continue connecting (yes/no)?
```
输入 `yes` 并回车。

**4. 输入密码**
提示 `root@47.100.xx.xx's password:` 时，输入你的实例密码。
⚠️ **注意**：输入密码时，终端**不会显示任何字符**（连星号都没有），这是正常的安全机制。输完直接回车即可。

**5. 连接成功**
看到类似 `Welcome to Alibaba Cloud Elastic Compute Service !` 或 `[root@iZbp... ~]#` 就说明成功了。

---

#### 场景 2：使用密钥（.pem 文件）登录

如果你创建实例时选择了“密钥对”，则不能用密码登录，需要用 `.pem` 文件。

**1. 找到你的密钥文件**
假设密钥文件路径是 `C:\Users\你的用户名\Downloads\mykey.pem`（Windows）或 `~/Downloads/mykey.pem`（Mac/Linux）。

**2. 输入命令**
```bash
ssh -i 密钥文件路径 登录名@公网IP
```
例如：
```bash
ssh -i ~/Downloads/mykey.pem root@47.100.xx.xx
```

**⚠️ Windows 用户特别注意：权限问题**
Windows 默认对 `.pem` 文件权限过于开放，SSH 会报错：
`Permissions for 'mykey.pem' are too open.`
**解决方法**：
1. 右键 `.pem` 文件 -> 属性 -> 安全 -> 高级。
2. 点击“禁用继承”，选择“将继承的权限转换为此对象的显式权限”。
3. 删掉除了你自己（当前用户）以外的所有用户。
4. 确定保存后，重新执行 SSH 命令。

**Mac/Linux 用户**：
需要修改密钥权限：
```bash
chmod 400 ~/Downloads/mykey.pem
```

---

### 三、不想每次都输一长串？配置 SSH 别名

如果你经常连这台机器，可以在本地配置 `~/.ssh/config`（Windows 在 `C:\Users\你的用户名\.ssh\config`）。

1. 创建或编辑该文件：
```bash
nano ~/.ssh/config
```
2. 写入：
```sshconfig
Host ali
    HostName 47.100.xx.xx
    User root
    Port 22
    IdentityFile ~/.ssh/mykey.pem
```
3. 以后只需要在终端输入：
```bash
ssh ali
```
就能直接连上。

---

### 四、终端连接常见报错及解决

| 报错信息                              | 原因         | 解决办法                                                     |
| :------------------------------------ | :----------- | :----------------------------------------------------------- |
| `Connection timed out`                | 网络不通     | 检查公网IP是否正确；检查阿里云**安全组**是否放行22端口；检查本地网络是否屏蔽了22端口。 |
| `Connection refused`                  | 服务没开     | 服务器上的 `sshd` 服务没启动，或者端口不是22。去阿里云控制台用 VNC 登录，检查 `systemctl status sshd`。 |
| `Permission denied (publickey)`       | 认证失败     | 密钥不对、用户名不对，或者服务器 `~/.ssh/authorized_keys` 权限不对。 |
| `Host key verification failed`        | 主机指纹变了 | 服务器重装过系统。执行 `ssh-keygen -R 你的公网IP` 清除旧记录，再重新连接。 |
| `Permission denied, please try again` | 密码错误     | 去阿里云控制台“重置实例密码”，重启实例后再试。               |

---

### 五、进阶：终端传文件

连上终端后，如果想传文件，可以在**本地新开一个终端**（不要在里面按 Ctrl+C 退出），使用：
```bash
# 上传本地文件到服务器
scp 本地文件路径 root@公网IP:/远程目录/

# 从服务器下载文件
scp root@公网IP:/远程文件路径 本地目录/
```
如果配了别名（如上面的 `ali`），也可以直接用：
```bash
scp 本地文件 ali:/root/
```

**总结**：日常用终端连阿里云，记住核心公式 `ssh 用户名@公网IP`，密钥登录加 `-i`。遇到连不上，第一反应查**安全组 22 端口**和**公网 IP**。