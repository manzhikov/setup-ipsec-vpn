﻿# 管理 VPN 用户

*其他语言版本: [English](manage-users.md), [简体中文](manage-users-zh.md).*

在默认情况下，将只创建一个用于 VPN 登录的用户账户。如果你需要添加，修改或者删除用户，请阅读本文档。

**注：** 同一个 VPN 账户可以在你的多个设备上使用。但是由于 IPsec 协议的局限性，如果这些设备在同一个 NAT 后面（比如家用路由器），它们无法同时连接到 VPN 服务器，即使你创建多个用户也是如此。对于上述情形，你可以尝试使用 [Shadowsocks](https://github.com/shadowsocks/shadowsocks-libev) / [ShadowsocksR](https://github.com/breakwa11/shadowsocks-rss) 或者 [OpenVPN](https://github.com/Nyr/openvpn-install)。

首先，IPsec PSK (预共享密钥) 保存在文件 `/etc/ipsec.secrets`。如果要更换一个新的 PSK，可以编辑此文件。

```bash
<VPN Server IP>  %any  : PSK "<VPN IPsec PSK>"
```

对于 `IPsec/L2TP`，VPN 用户账户信息保存在文件 `/etc/ppp/chap-secrets`。该文件的格式如下：

```bash
"<VPN User 1>"  l2tpd  "<VPN Password 1>"  *
"<VPN User 2>"  l2tpd  "<VPN Password 2>"  *
... ...
```

你可以添加更多用户，每个用户对应文件中的一行。**不要**在用户名，密码或 PSK 中使用这些字符：`\ " '`

对于 `IPsec/XAuth ("Cisco IPsec")`， VPN 用户账户信息保存在文件 `/etc/ipsec.d/passwd`。该文件的格式如下：

```bash
<VPN User 1>:<VPN Password 1 (hashed)>:xauth-psk
<VPN User 2>:<VPN Password 2 (hashed)>:xauth-psk
... ...
```

这个文件中的密码以 salted and hashed 的形式保存。该步骤可以借助比如 `openssl` 工具来完成：

```bash
# 以下命令的输出为 <VPN Password 1 (hashed)>
openssl passwd -1 "<VPN Password 1>"
```

在完成修改之后，运行以下命令或者重启服务器。

```bash
service ipsec restart
service xl2tpd restart
```
