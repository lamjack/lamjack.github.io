---
title: Linux 常用命令汇总
date: 2021-03-31 11:37:57
tags:
  - linux
---

## 服务管理 Systemd

```bash
# 列出所有服务
systemctl list-unit-files --no-pager

# 模糊查询服务名称
systemctl list-unit-files --no-pager | grep redis
```

## 防火墙 Iptable

1. 查看当前 iptables 状态
    ```bash
    # 默认查看 filter 表的状态，如果需要查看其他表的状态加上 -t 表名
    iptables -nL
      
    # 可以列出序列号，在插入或者删除的时候就不用自己去数了
    iptables -nL --line-numbers #可以列出序列号，在插入或者删除的时候就不用自己去数了
      
    # 可以查看到包过滤的流量统计，访问次数等
    iptables -nL --line-numbers --verbose
    ```
2. 插入一条记录
    ```bash
    # 在第一条的位置插入一条记录，接受所有来自 lo 网口的访问
    iptables -I INPUT 1 -i lo -j ACCPET

    # 如果在 INPUT 中不指明在第几条插入，默认就是在第一条插入
    iptables -I INPUT 2 -s 192.168.1.0/24 -j ACCPET
    ```
3. 追加一条记录
    ```bash
    # 在 INPUT 最后追加一条记录
    iptables -A INPUT -s 192.168.2.0/24 -j ACCEPT
    ```
4. 删除一条记录
    ```bash
    # 删除第 7 条记录
    iptables -D INPUT 7
    ```
5. 针对协议开放
    ```bash
    iptables -I INPUT -p imcp -j ACCEPT
    ```
6. 针对端口开放（需要指明协议）
    ```bash
    iptables -I INPUT -p tcp --dport 22 -j ACCEPT
    ```
7. 限制 ip 端口访问
    ```bash
    iptables -I INPUT -s 192.168.1.0/24 -p tcp --dport 22 -j ACCPET
    ```
8. 拒绝所有访问
    ```bash
    # 这个一般放到最后，不然会对前面的规则造成影响
    iptables -A INPUT -j DROP
    ```
9. 根据时段限制访问
    ```bash
    # 这里的时间是指 UTC 时间记得换算
    iptables -A INPUT -p tcp -m time --timestart 00:00 --timestop 02:00 -j DROP
    ```
10. 限制单个 IP 一分钟内建立的连接数
    ```bash
    iptables -A INPUT -p tcp --syn --dport 80 -m connlimit --connlimit-above 25 -j REJECT
    ```
11. 保存 iptables 规则
    ```bash
    iptables-save > /etc/sysconfig/iptables
    ```
12. 从文件里面恢复 iptables 规则
    ```bash
    iptables-restore < /etc/sysconfig/iptables
    ```
13. 对外建立的连接经过 INPUT 不拦截
    ```bash
    iptables -I INPUT -m conntrack --ctstate RELATED,ESTABLISHED -j ACCEPT
    ```
14. 端口转发（本机 8080 转发到远程 192.168.1.22:80）
    ```bash
    iptables -t nat -A PREROUTING -p tcp -i eth0 --dport 8080 -j DNAT --to 192.168.1.22:80
    iptables -t nat -A POSTROUTING -j MASQUERADE
    # 需要打开网转发
    echo 1 > /proc/sys/net/ipv4/ip_forward
    # 转发的 FROWARD 要允许双方的数据传输
    iptables -t filter FORWARD -d 192.168.1.22/32 -j ACCEPT
    iptables -t filter FORWARD -s 192.168.1.22/32 -j ACCEPT
    ```