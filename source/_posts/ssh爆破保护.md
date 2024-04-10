---
title: ssh爆破攻击
date: 2024-04-10 13:33:36
tags:
---

## ssh爆破防范

### fail2ban安装

```shell
apt update
apt install fail2ban
systemctl enable fail2ban.service
```

### 配置fail2ban

```shell
vim /etc/fail2ban/jail.conf
```

修改配置项

```shell
bantime  = 8760h # 1 year
findtime = 43200m # 1 month 
maxretry = 2 # 2 times
```

重启fail2ban

```shell
systemctl restart fail2ban.service
```

查看sshd状态

```shell
fail2ban-client status sshd
```