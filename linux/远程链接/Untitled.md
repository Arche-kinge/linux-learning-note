Linux 远程连接方式很多，通常按用途分为：**命令行、图形桌面、文件传输、带外/Web 管理、网络层接入**。

## 1. 命令行/终端远程
| 方式                       | 默认端口/协议              | 说明                                                         |
| -------------------------- | -------------------------- | ------------------------------------------------------------ |
| **SSH**                    | 22/TCP                     | Linux 远程管理事实标准。加密，支持密钥、端口转发、SFTP、SCP、X11 转发。 |
| **Mosh**                   | 60000-61000/UDP            | 基于 SSH 认证，抗断网、抗漫游，适合移动网络。                |
| **Telnet**                 | 23/TCP                     | 明文传输，不安全，已基本淘汰。                               |
| **rlogin / rsh / rexec**   | 513/514 等                 | 古老且明文，不推荐。                                         |
| **串口 Console**           | /dev/ttyS0 等              | 用 minicom、picocom、screen 连接串口，适合无网络/救援。      |
| **IPMI SOL / iDRAC / iLO** | 623/UDP、443 等            | 服务器带外管理，可远程 KVM、开关机、串口重定向。             |
| **容器内远程**             | docker exec / kubectl exec | 进入容器或 Pod，不是传统远程登录，但常作远程操作。           |

## 2. 图形桌面远程
| 方式               | 默认端口      | 说明                                                         |
| ------------------ | ------------- | ------------------------------------------------------------ |
| **VNC**            | 5900+         | TigerVNC、TightVNC、x11vnc 等。跨平台，但本身加密弱，建议走 SSH 隧道或 VPN。 |
| **RDP**            | 3389/TCP、UDP | Linux 服务端常用 **xrdp**；客户端可用 Windows 远程桌面、FreeRDP、Remmina。 |
| **X11 Forwarding** | 通过 SSH 22   | `ssh -X` 或 `ssh -Y`，只转发单个图形程序，不是完整桌面。     |
| **X2Go**           | 基于 SSH      | 基于 NX 协议，远程 Linux 桌面体验较好。                      |
| **NoMachine NX**   | 4000 等       | 商业/免费版，图形体验好。                                    |
| **SPICE**          | 常用于 KVM    | 主要用于 KVM/QEMU 虚拟机远程桌面。                           |
| **XDMCP**          | 177/UDP       | 很旧，不安全，不推荐。                                       |
| **第三方远控**     | 自定义        | TeamViewer、AnyDesk、RustDesk、Chrome Remote Desktop、向日葵等。 |
| **游戏串流**       | 自定义        | Sunshine + Moonlight 等。                                    |

## 3. 文件传输/同步
| 方式               | 端口    | 说明                              |
| ------------------ | ------- | --------------------------------- |
| **SFTP / SCP**     | 22/TCP  | 基于 SSH，安全，常用。            |
| **rsync over SSH** | 22/TCP  | 增量同步，适合备份。              |
| **rsync daemon**   | 873/TCP | 独立 rsync 服务，注意权限和加密。 |
| **FTP / FTPS**     | 21/TCP  | FTP 明文，不推荐；FTPS 是加密版。 |
| **NFS**            | 2049    | Linux 间共享文件，通常内网使用。  |
| **SMB/CIFS**       | 445/TCP | 与 Windows 共享互操作。           |
| **WebDAV**         | 80/443  | 基于 HTTP 的文件访问。            |

## 4. Web 管理/云控制台
- **Cockpit**：9090/TCP，浏览器管理 Linux。
- **Webmin**：10000/TCP，Web 管理面板。
- **云厂商控制台**：阿里云、腾讯云、AWS、Azure 等提供的 VNC/串口控制台。
- **VPN 后接入**：WireGuard、OpenVPN、IPsec、Tailscale、ZeroTier 等，先接入内网再连 SSH/VNC/RDP。

## 5. 常用推荐
- **纯命令行**：SSH + 密钥认证；网络不稳用 Mosh。
- **图形桌面**：xrdp + RDP，或 VNC over SSH；体验好可用 NoMachine/X2Go。
- **文件传输**：SFTP、SCP、rsync。
- **Web 管理**：Cockpit。
- **无网络/救援**：串口 Console、IPMI/iDRAC/iLO。
- **公网安全**：不要直接暴露 Telnet、VNC、RDP；优先 VPN/Tailscale/ZeroTier + SSH。

总结：最核心的是 **SSH**；图形远程常用 **VNC、RDP/xrdp、X2Go、NoMachine**；文件传输用 **SFTP/SCP/rsync**；带外管理用 **IPMI/iDRAC/iLO/串口**。