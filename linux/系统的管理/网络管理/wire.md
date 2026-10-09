bash

```
ip a                     # 查看 IP
ip r                     # 路由
ss -tuln                 # 监听端口
ping 8.8.8.8
traceroute 8.8.8.8
curl -I https://example.com
dig example.com
nmcli device status      # NetworkManager
nmtui                    # 文本图形网络配置
cat /etc/hosts
cat /etc/resolv.conf
```



防火墙：

bash

```
# firewalld
firewall-cmd --list-all
firewall-cmd --add-service=http --permanent
firewall-cmd --reload

# ufw
ufw status
ufw allow 22/tcp
ufw enable
```