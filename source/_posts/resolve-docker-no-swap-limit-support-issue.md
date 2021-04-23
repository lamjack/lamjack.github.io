---
title: Resolve docker No swap limit support issue
date: 2021-04-23 12:34:01
categories:
tags:
  - docker
  - ubuntu
---

## 解决方法

此方法只在 ubuntu 20.04 测试过，请谨慎使用。

1. 编辑 `/etc/default/grub` 文件，找到下面这一行，在原有内容后添加 `cgroup_enable=memory swapaccount=1`

   ```
   ...
   GRUB_CMDLINE_LINUX="vga=792 console=tty0 console=ttyS0,115200n8 net.ifnames=0 noibrs cgroup_enable=memory swapaccount=1"
   ...
   ```

2. 保存编辑文件，并更新 grub

   ```bash
   sudo update-grub
   ```

3. 重启服务器

