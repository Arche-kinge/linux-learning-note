## curl

（client for url）意思是“URL 客户端”或“用于 URL 的客户端工具”。



url（**Uniform Resource Locator**
中文通常翻译为：**统一资源定位符**（也叫统一资源定位器）。）













---

## wget

（ **World Wide Web + get** | **GNU Wget**）从万维网获取文件



~~~zsh
**wget** 通常被认为是 **World Wide Web + get** 的合成词，意思是“从万维网获取文件”。  
它的官方名称是 **GNU Wget**，是 GNU 项目下的一个免费命令行下载工具。

简单说：

> **wget 就是一个专门用来从网络下载文件的命令行工具。**

例如：

```bash
wget https://example.com/file.zip
```

它会把 `file.zip` 下载到当前目录。

### wget 的特点

- 支持 **HTTP、HTTPS、FTP** 等协议
- 支持 **断点续传**：下载中断后可以继续
- 支持 **递归下载**：可以整站镜像
- 支持 **后台下载**
- 非交互式：适合脚本和服务器环境
- 非常稳定，很多 Linux 发行版默认自带

### 常用参数

```bash
wget URL                  # 下载文件
wget -O newname.zip URL   # 下载并重命名为 newname.zip
wget -c URL               # 断点续传
wget -q URL               # 安静模式，不输出信息
wget -r -np -k URL        # 递归下载网站，不追父目录，转换链接
wget -b URL               # 后台下载
```

### 和 curl 的区别

| 工具 | 全称/含义 | 主要用途 |
|---|---|---|
| `curl` | Client URL | 网络请求工具，能下载、上传、调 API、发 POST 等 |
| `wget` | World Wide Web get | 专门下载文件，递归下载和断点续传更强 |

可以这样记：

- **curl**：更像“网络请求万能工具”
- **wget**：更像“文件下载器”

在 Docker 安装里，如果系统没有 `curl`，有时也可以用 `wget` 替代：

```bash
wget -qO- https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
```

安装 wget：

```bash
sudo apt install wget        # Ubuntu / Debian
sudo dnf install wget        # Fedora / RHEL 新版
sudo yum install wget        # CentOS / RHEL 旧版
```

所以，**wget 就是 Linux 里最常用的命令行下载工具之一**，全称可以理解为“从万维网获取”。
~~~







---

## git clone



`git clone` 不是一个像 `curl`、`wget` 那样的“下载工具全称”，它是 **Git 的一个子命令**。

- **git**：一个分布式版本控制系统，名字就叫 Git，官方没有正式全称，有人戏称它是 “Global Information Tracker”，但这不是官方定义。
- **clone**：英文“克隆、复制”的意思。
- **git clone**：直译就是“用 Git 克隆一个远程仓库到本地”。

所以可以理解为：

> **git clone 就是把远程 Git 仓库完整复制到本地，并保留版本控制信息。**

例如：

```bash
git clone https://github.com/octocat/Hello-World.git
```

它会在当前目录下创建一个 `Hello-World` 文件夹，里面包含：

- 项目的所有文件
- `.git` 目录，里面有提交历史、分支、标签等信息
- 自动配置好远程仓库地址，通常叫 `origin`
- 默认检出主分支

之后你就可以：

```bash
cd Hello-World
git log
git branch
git pull
```

### 和 curl / wget 的区别

| 工具        | 作用                                            |
| ----------- | ----------------------------------------------- |
| `curl`      | 发网络请求，可下载、上传、调 API                |
| `wget`      | 专门下载文件，支持断点续传、递归下载            |
| `git clone` | 克隆整个 Git 仓库，包含版本历史，不只是下载文件 |

简单说：

- `wget` 下载的是一个文件或网页。
- `git clone` 下载的是一个“代码仓库”，并且带有完整的 Git 历史，之后可以继续拉取更新、切换分支、提交代码。

### 常用写法

```bash
git clone <仓库地址>                # 克隆到同名目录
git clone <仓库地址> mydir          # 克隆到指定目录
git clone -b main <仓库地址>        # 只克隆 main 分支
git clone --depth 1 <仓库地址>      # 浅克隆，只取最近一次提交，速度快
git clone --recurse-submodules <仓库地址>  # 同时克隆子模块
```

仓库地址常见两种：

```bash
https://github.com/user/repo.git
git@github.com:user/repo.git
```

前者用 HTTPS，后者用 SSH。

如果系统没有 Git，安装：

```bash
sudo apt install git        # Ubuntu / Debian
sudo dnf install git        # Fedora / RHEL 新版
```

总结：**`git clone` 就是“用 Git 把远程代码仓库克隆到本地”**，它比 `wget`、`curl` 更偏向代码版本管理，而不是单纯下载文件。



---

## apt









---

## docker pull



