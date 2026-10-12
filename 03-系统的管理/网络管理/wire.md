来，一份 **Linux 网络管理速查 + 命令选项详细注释**。  
网络管理核心就几类：**看接口、配 IP、配路由、查 DNS、测连通、看端口、抓包、防火墙、持久化配置**。

---

## 1. 网络接口管理：`ip`

`ip` 是现代 Linux 最核心的网络命令，替代老旧的 `ifconfig`、`route`、`arp`。

### 查看接口和 IP
```bash
ip addr show
ip a
ip -br a
ip -4 a
ip -6 a
ip -s link
```

| 选项         | 含义                         |
| ------------ | ---------------------------- |
| `a` / `addr` | 查看 IP 地址                 |
| `link`       | 查看链路层信息，如 MAC、状态 |
| `-br`        | brief，简洁输出              |
| `-4`         | 只看 IPv4                    |
| `-6`         | 只看 IPv6                    |
| `-s`         | statistics，显示收发包统计   |
| `-c`         | color，彩色输出              |
| `-o`         | oneline，一行显示            |
| `-j`         | json，JSON 格式              |
| `-d`         | details，显示详细信息        |
| `-h`         | help，帮助                   |
| `-V`         | version，版本                |

示例：
```bash
ip -br a
ip -s link show eth0
```

### 启停接口
```bash
ip link set eth0 up
ip link set eth0 down
ip link set eth0 mtu 1500
ip link set eth0 promisc on
```

| 选项             | 含义              |
| ---------------- | ----------------- |
| `set`            | 设置接口          |
| `up`             | 启用接口          |
| `down`           | 关闭接口          |
| `mtu`            | 设置最大传输单元  |
| `promisc on/off` | 开启/关闭混杂模式 |
| `address`        | 修改 MAC 地址     |

### 临时配置 IP
```bash
ip addr add 192.168.1.100/24 dev eth0
ip addr del 192.168.1.100/24 dev eth0
ip addr flush dev eth0
```

| 选项    | 含义            |
| ------- | --------------- |
| `add`   | 添加 IP         |
| `del`   | 删除 IP         |
| `flush` | 清空接口所有 IP |
| `dev`   | 指定网络接口    |

### 路由管理
```bash
ip route show
ip route add default via 192.168.1.1 dev eth0
ip route del default
ip route add 10.0.0.0/24 via 192.168.1.1 dev eth0
ip neigh
```

| 选项      | 含义            |
| --------- | --------------- |
| `route`   | 路由表          |
| `show`    | 查看            |
| `add`     | 添加路由        |
| `del`     | 删除路由        |
| `default` | 默认路由        |
| `via`     | 下一跳网关      |
| `dev`     | 出接口          |
| `neigh`   | 查看 ARP/邻居表 |

---

## 2. 旧命令：`ifconfig`、`route`、`arp`

有些老系统还在用。

```bash
ifconfig
ifconfig eth0 up
ifconfig eth0 down
ifconfig eth0 192.168.1.100 netmask 255.255.255.0
```

| 选项      | 含义         |
| --------- | ------------ |
| `-a`      | 显示所有接口 |
| `-s`      | 简短统计     |
| `up`      | 启用         |
| `down`    | 关闭         |
| `netmask` | 子网掩码     |
| `mtu`     | 设置 MTU     |

```bash
route -n
route add default gw 192.168.1.1
route del default
arp -a
```

| 选项         | 含义        |
| ------------ | ----------- |
| `-n`         | 不解析域名  |
| `add`        | 添加        |
| `del`        | 删除        |
| `default gw` | 默认网关    |
| `arp -a`     | 查看 ARP 表 |

---

## 3. NetworkManager：`nmcli` / `nmtui`

桌面版、Ubuntu、RHEL/CentOS 常用。

```bash
nmcli general status
nmcli networking on
nmcli networking off
nmcli radio wifi on
nmcli radio wifi off
```

| 选项         | 含义       |
| ------------ | ---------- |
| `general`    | 总体状态   |
| `networking` | 网络总开关 |
| `radio`      | 无线开关   |
| `wifi`       | Wi-Fi 控制 |
| `on/off`     | 开启/关闭  |

### 连接管理
```bash
nmcli connection show
nmcli connection up eth0
nmcli connection down eth0
nmcli connection modify eth0 ipv4.addresses 192.168.1.100/24
nmcli connection modify eth0 ipv4.gateway 192.168.1.1
nmcli connection modify eth0 ipv4.dns "8.8.8.8 1.1.1.1"
nmcli connection modify eth0 ipv4.method manual
nmcli connection up eth0
```

| 选项                 | 含义                       |
| -------------------- | -------------------------- |
| `connection` / `con` | 连接配置                   |
| `show`               | 查看连接                   |
| `up`                 | 启用连接                   |
| `down`               | 关闭连接                   |
| `modify`             | 修改连接                   |
| `ipv4.addresses`     | IPv4 地址                  |
| `ipv4.gateway`       | IPv4 网关                  |
| `ipv4.dns`           | DNS                        |
| `ipv4.method`        | `manual` 静态，`auto` DHCP |

### 设备管理
```bash
nmcli device status
nmcli device disconnect eth0
nmcli device wifi list
nmcli device wifi connect SSID password PASS
```

| 选项             | 含义       |
| ---------------- | ---------- |
| `device` / `dev` | 网络设备   |
| `status`         | 设备状态   |
| `disconnect`     | 断开设备   |
| `wifi list`      | 扫描 Wi-Fi |
| `wifi connect`   | 连接 Wi-Fi |

### `nmcli` 全局选项
| 选项 | 含义                           |
| ---- | ------------------------------ |
| `-t` | terse，简洁输出                |
| `-p` | pretty，美化输出               |
| `-f` | fields，指定字段               |
| `-g` | get values，取值               |
| `-m` | mode，`tabular` 或 `multiline` |
| `-s` | show secrets，显示密码         |
| `-a` | ask，交互询问                  |
| `-w` | wait，超时时间                 |
| `-v` | version，版本                  |

文本界面：
```bash
nmtui
```

---

## 4. DNS 查询：`dig`、`nslookup`、`host`

### `dig`
```bash
dig www.baidu.com
dig @8.8.8.8 www.baidu.com
dig +short www.baidu.com
dig -x 8.8.8.8
dig +trace www.baidu.com
```

| 选项             | 含义                                |
| ---------------- | ----------------------------------- |
| `@服务器`        | 指定 DNS 服务器                     |
| `+short`         | 只显示结果                          |
| `-t 类型`        | 查询类型，如 A、AAAA、MX、NS、CNAME |
| `-x IP`          | 反向解析                            |
| `+trace`         | 从根开始追踪解析                    |
| `+noall +answer` | 只显示答案                          |
| `+tcp`           | 使用 TCP 查询                       |
| `+dnssec`        | 显示 DNSSEC 信息                    |
| `-p 端口`        | 指定端口                            |
| `-4` / `-6`      | 强制 IPv4 / IPv6                    |

### `nslookup`
```bash
nslookup www.baidu.com
nslookup www.baidu.com 8.8.8.8
nslookup -type=mx baidu.com
```

| 选项     | 含义            |
| -------- | --------------- |
| `-type=` | 查询类型        |
| `server` | 指定 DNS 服务器 |

### `host`
```bash
host www.baidu.com
host 8.8.8.8
host -t mx baidu.com
```

| 选项        | 含义             |
| ----------- | ---------------- |
| `-t`        | 查询类型         |
| `-v`        | 详细输出         |
| `-4` / `-6` | 强制 IPv4 / IPv6 |

---

## 5. 连通性测试：`ping`、`traceroute`、`mtr`

### `ping`
```bash
ping -c 4 www.baidu.com
ping -i 0.5 8.8.8.8
ping -I eth0 8.8.8.8
ping -s 1472 8.8.8.8
```

| 选项        | 含义                  |
| ----------- | --------------------- |
| `-c`        | count，发送次数       |
| `-i`        | interval，间隔秒数    |
| `-W`        | 等待响应超时，秒      |
| `-w`        | 总超时时间，秒        |
| `-s`        | 包大小，字节          |
| `-I`        | 指定接口或源地址      |
| `-4` / `-6` | IPv4 / IPv6           |
| `-n`        | 不解析域名            |
| `-q`        | 安静模式              |
| `-f`        | flood，快速发包，慎用 |
| `-M`        | 路径 MTU 发现         |
| `-t`        | TTL                   |
| `-D`        | 显示时间戳            |

### `traceroute`
```bash
traceroute -n www.baidu.com
traceroute -I www.baidu.com
traceroute -T -p 443 www.baidu.com
```

| 选项        | 含义         |
| ----------- | ------------ |
| `-n`        | 不解析域名   |
| `-m`        | 最大跳数     |
| `-w`        | 等待超时     |
| `-q`        | 每跳查询次数 |
| `-I`        | 使用 ICMP    |
| `-T`        | 使用 TCP     |
| `-U`        | 使用 UDP     |
| `-p`        | 目标端口     |
| `-s`        | 源地址       |
| `-i`        | 指定接口     |
| `-4` / `-6` | IPv4 / IPv6  |

### `mtr`
```bash
mtr -r -c 10 www.baidu.com
mtr -n www.baidu.com
```

| 选项        | 含义             |
| ----------- | ---------------- |
| `-r`        | report，报告模式 |
| `-c`        | 发送次数         |
| `-n`        | 不解析域名       |
| `-i`        | 间隔             |
| `-p`        | 端口             |
| `-T`        | TCP              |
| `-u`        | UDP              |
| `-4` / `-6` | IPv4 / IPv6      |

---

## 6. 端口和连接：`ss`、`netstat`

### `ss`
现代替代 `netstat`。

```bash
ss -tulnp
ss -tan
ss -s
ss -tp
```

| 选项        | 含义                  |
| ----------- | --------------------- |
| `-t`        | TCP                   |
| `-u`        | UDP                   |
| `-l`        | listening，监听       |
| `-a`        | all，全部             |
| `-n`        | numeric，不解析服务名 |
| `-p`        | process，显示进程     |
| `-s`        | summary，统计         |
| `-4` / `-6` | IPv4 / IPv6           |
| `-o`        | timers，定时器        |
| `-e`        | extended，扩展信息    |
| `-i`        | internal，内部信息    |
| `-H`        | no header，不显示标题 |
| `-Z`        | SELinux 上下文        |
| `-K`        | kill，关闭套接字      |

示例：
```bash
ss -tulnp | grep :80
```

### `netstat`
老命令，部分系统需安装 `net-tools`。

```bash
netstat -tulnp
netstat -rn
netstat -an
```

| 选项 | 含义     |
| ---- | -------- |
| `-t` | TCP      |
| `-u` | UDP      |
| `-l` | 监听     |
| `-n` | 不解析   |
| `-p` | 进程     |
| `-a` | 全部     |
| `-r` | 路由     |
| `-i` | 接口     |
| `-s` | 统计     |
| `-c` | 持续输出 |

---

## 7. 端口测试：`nc`、`telnet`

### `nc`
```bash
nc -zv 192.168.1.1 22
nc -zv 192.168.1.1 20-30
nc -l 8080
nc -u 192.168.1.1 53
```

| 选项 | 含义               |
| ---- | ------------------ |
| `-z` | 只扫描，不发送数据 |
| `-v` | 详细输出           |
| `-l` | 监听模式           |
| `-p` | 指定端口           |
| `-u` | UDP                |
| `-w` | 超时               |
| `-k` | 保持监听           |
| `-n` | 不解析域名         |

### `telnet`
```bash
telnet 192.168.1.1 22
```

| 选项        | 含义        |
| ----------- | ----------- |
| `-4` / `-6` | IPv4 / IPv6 |
| `-l`        | 指定用户名  |
| `-p`        | 指定端口    |

---

## 8. HTTP 和下载：`curl`、`wget`

### `curl`
```bash
curl -I https://www.baidu.com
curl -v https://www.baidu.com
curl -X POST -H "Content-Type: application/json" -d '{"a":1}' https://httpbin.org/post
curl -o file.html https://www.baidu.com
curl -L https://example.com
```

| 选项                | 含义           |
| ------------------- | -------------- |
| `-I`                | 只取响应头     |
| `-i`                | 包含响应头     |
| `-v`                | 详细输出       |
| `-X`                | 指定 HTTP 方法 |
| `-H`                | 添加请求头     |
| `-d`                | 发送数据       |
| `-F`                | 表单上传       |
| `-u`                | 用户名密码     |
| `-k`                | 跳过证书验证   |
| `-L`                | 跟随重定向     |
| `-o`                | 输出到文件     |
| `-O`                | 使用远程文件名 |
| `-s`                | 静默           |
| `-S`                | 显示错误       |
| `-w`                | 自定义输出格式 |
| `--connect-timeout` | 连接超时       |
| `--max-time`        | 总超时         |
| `-x`                | 使用代理       |
| `-b`                | 发送 Cookie    |
| `-c`                | 保存 Cookie    |

### `wget`
```bash
wget https://example.com/file.tar.gz
wget -c https://example.com/file.tar.gz
wget -r -np https://example.com/dir/
wget --spider https://example.com
```

| 选项                     | 含义           |
| ------------------------ | -------------- |
| `-O`                     | 输出文件名     |
| `-c`                     | 断点续传       |
| `-r`                     | 递归下载       |
| `-np`                    | 不进入父目录   |
| `-q`                     | 安静           |
| `--limit-rate`           | 限速           |
| `--spider`               | 只检查链接     |
| `-P`                     | 保存目录       |
| `-N`                     | 时间戳         |
| `--no-check-certificate` | 跳过证书       |
| `-U`                     | 用户代理       |
| `-b`                     | 后台下载       |
| `-i`                     | 从文件读取 URL |

---

## 9. 抓包：`tcpdump`

```bash
tcpdump -i eth0
tcpdump -i eth0 -nn
tcpdump -i eth0 port 80
tcpdump -i eth0 host 192.168.1.1
tcpdump -i eth0 -w capture.pcap
tcpdump -r capture.pcap
```

| 选项                  | 含义             |
| --------------------- | ---------------- |
| `-i`                  | 指定接口         |
| `-n`                  | 不解析域名       |
| `-nn`                 | 不解析域名和端口 |
| `-v` / `-vv` / `-vvv` | 详细级别         |
| `-c`                  | 抓包数量         |
| `-w`                  | 写入文件         |
| `-r`                  | 读取文件         |
| `-A`                  | ASCII 显示       |
| `-X`                  | 十六进制 + ASCII |
| `-s`                  | 抓包长度         |
| `-e`                  | 显示链路层头     |
| `-p`                  | 不进入混杂模式   |
| `-q`                  | 快速输出         |
| `-D`                  | 列出接口         |
| `-l`                  | 行缓冲           |
| `-Z`                  | 指定用户         |

过滤表达式：
```bash
tcpdump -i eth0 tcp port 80
tcpdump -i eth0 src 192.168.1.10
tcpdump -i eth0 dst port 53
tcpdump -i eth0 net 192.168.1.0/24
tcpdump -i eth0 'tcp and port 443'
```

| 表达式                         | 含义     |
| ------------------------------ | -------- |
| `host`                         | 主机     |
| `port`                         | 端口     |
| `net`                          | 网段     |
| `src`                          | 源       |
| `dst`                          | 目的     |
| `tcp` / `udp` / `icmp` / `arp` | 协议     |
| `and` / `or` / `not`           | 逻辑组合 |

---

## 10. 防火墙

### `ufw`：Ubuntu/Debian 简单防火墙
```bash
ufw status
ufw enable
ufw disable
ufw allow 22/tcp
ufw deny 80/tcp
ufw delete allow 22/tcp
ufw limit ssh
ufw reset
```

| 选项      | 含义             |
| --------- | ---------------- |
| `enable`  | 启用             |
| `disable` | 禁用             |
| `status`  | 状态             |
| `allow`   | 允许             |
| `deny`    | 拒绝             |
| `reject`  | 拒绝并回复       |
| `limit`   | 限速，防暴力破解 |
| `delete`  | 删除规则         |
| `reset`   | 重置             |
| `default` | 默认策略         |
| `logging` | 日志             |

### `firewall-cmd`：RHEL/CentOS/Fedora
```bash
firewall-cmd --state
firewall-cmd --list-all
firewall-cmd --zone=public --add-port=80/tcp --permanent
firewall-cmd --zone=public --add-service=http --permanent
firewall-cmd --reload
firewall-cmd --remove-port=80/tcp --permanent
```

| 选项                 | 含义         |
| -------------------- | ------------ |
| `--state`            | 运行状态     |
| `--list-all`         | 列出所有规则 |
| `--zone`             | 指定区域     |
| `--add-port`         | 添加端口     |
| `--remove-port`      | 删除端口     |
| `--add-service`      | 添加服务     |
| `--permanent`        | 永久生效     |
| `--reload`           | 重载         |
| `--get-active-zones` | 活动区域     |
| `--change-interface` | 修改接口区域 |
| `--query-port`       | 查询端口     |

### `iptables` / `nftables`
```bash
iptables -L -n -v
iptables -A INPUT -p tcp --dport 22 -j ACCEPT
iptables -A INPUT -p tcp --dport 80 -j DROP
iptables -D INPUT -p tcp --dport 80 -j DROP
iptables -F
iptables -t nat -L -n
```

| 选项      | 含义                     |
| --------- | ------------------------ |
| `-L`      | 列出规则                 |
| `-n`      | 不解析                   |
| `-v`      | 详细                     |
| `-A`      | 追加规则                 |
| `-I`      | 插入规则                 |
| `-D`      | 删除规则                 |
| `-P`      | 默认策略                 |
| `-F`      | 清空规则                 |
| `-t`      | 指定表，如 nat           |
| `-p`      | 协议                     |
| `--dport` | 目标端口                 |
| `-j`      | 动作，ACCEPT/DROP/REJECT |
| `-s`      | 源地址                   |
| `-d`      | 目的地址                 |
| `-i`      | 入接口                   |
| `-o`      | 出接口                   |

`nft`：
```bash
nft list ruleset
nft add rule inet filter input tcp dport 22 accept
nft delete rule inet filter input handle 3
```

---

## 11. 持久化网络配置

### Ubuntu：`netplan`
配置文件：`/etc/netplan/*.yaml`

```yaml
network:
  version: 2
  ethernets:
    eth0:
      dhcp4: no
      addresses:
        - 192.168.1.100/24
      routes:
        - to: default
          via: 192.168.1.1
      nameservers:
        addresses:
          - 8.8.8.8
          - 1.1.1.1
```

应用：
```bash
netplan try
netplan apply
```

| 选项       | 含义                   |
| ---------- | ---------------------- |
| `try`      | 测试配置，出错自动回滚 |
| `apply`    | 应用配置               |
| `generate` | 生成后端配置           |
| `--debug`  | 调试                   |

### RHEL/CentOS：`ifcfg`
文件：`/etc/sysconfig/network-scripts/ifcfg-eth0`

```bash
TYPE=Ethernet
BOOTPROTO=static
IPADDR=192.168.1.100
NETMASK=255.255.255.0
GATEWAY=192.168.1.1
DNS1=8.8.8.8
ONBOOT=yes
```

重启：
```bash
systemctl restart network
# 或
nmcli connection reload
nmcli connection up eth0
```

### systemd-networkd
配置文件：`/etc/systemd/network/*.network`

```ini
[Match]
Name=eth0

[Network]
Address=192.168.1.100/24
Gateway=192.168.1.1
DNS=8.8.8.8
```

```bash
systemctl enable --now systemd-networkd
systemctl restart systemd-networkd
```

---

## 12. 常见任务速查

| 任务           | 命令                                     |
| -------------- | ---------------------------------------- |
| 看 IP          | `ip -br a`                               |
| 看路由         | `ip r`                                   |
| 启用网卡       | `ip link set eth0 up`                    |
| 临时配 IP      | `ip addr add 192.168.1.100/24 dev eth0`  |
| 加默认网关     | `ip route add default via 192.168.1.1`   |
| 看监听端口     | `ss -tulnp`                              |
| 测连通         | `ping -c 4 8.8.8.8`                      |
| 查 DNS         | `dig +short www.baidu.com`               |
| 追踪路由       | `traceroute -n www.baidu.com`            |
| 抓包           | `tcpdump -i eth0 -nn port 80`            |
| HTTP 测试      | `curl -I https://www.baidu.com`          |
| 下载           | `wget -c URL`                            |
| 防火墙状态     | `ufw status` / `firewall-cmd --list-all` |
| NetworkManager | `nmcli device status`                    |
| 图形化网络     | `nmtui`                                  |

---

## 13. 排错基本顺序

```bash
ip -br a                 # 有没有 IP
ip r                     # 有没有默认路由
ping 网关                # 通不通网关
ping 8.8.8.8             # 通不通外网
dig www.baidu.com        # DNS 正不正常
ss -tulnp                # 服务有没有监听
traceroute -n 目标        # 路径卡在哪
tcpdump -i eth0 -nn      # 抓包看流量
ufw status               # 防火墙有没有拦
```

---

一句话总结：  
**现代 Linux 网络管理优先用 `ip`、`ss`、`nmcli`、`dig`、`curl`、`tcpdump`；老命令 `ifconfig`、`netstat`、`route` 知道即可。配置持久化看发行版：Ubuntu 多用 netplan，RHEL 多用 NetworkManager / ifcfg，systemd 系统可用 systemd-networkd。**