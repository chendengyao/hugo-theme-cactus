---
title: Arch Linux基本配置
date: 2023-11-19 15:10:18
categories: blog
tags: 
- Arch Linux
- linux
keywords: Arch
---

# 新系统配置

## 激活安装的服务

1. 打开安装的服务

    ``` bash
        systemctl start iwd.service
        systemctl enable iwd.service
        systemctl start systemd-resolved.service
        systemctl enable systemd-resolved.service
        systemctl enable bluetooth.service
        systemctl enable NetworkManager
        systemctl enable dhcpcd
    ```

2. 配置网络连接和 DNS

   输入` vim /etc/iwd/main.conf `，把下面的文字打进去，保存。

   ```text
   [General]
   EnableNetworkConfiguration=true
   NameResolvingService=systemd
   ```

3. 安装了 netwokmanager 的配置

   如果安装了networkmanager，则需要将backend服务修改为iwd，总体上iwd比wpa好用很多。

   输入“vim /etc/NetworkManager/NetworkManager.conf”，把下面的文字打进去，保存。（vim的光标移动、删除和保存退出等命令请另行百度。）

   ```text
   [device]
   wifi.backend=iwd
   ```

​	然后使用 `iwctl` 进行联网并配置镜像源，可参考安装的文章

## 安装驱动

1. CUP编码

   ``` 
   pacman -S intel-ucode    (intel的cpu装这个)
   pacman -S amd-ucode    (amd的cpu装这个)
   ```

2. 显卡驱动

   ```
   pacman -S xf86-video-intel（Intel核心显卡驱动，用Intel核显就装，否则不用装）
   pacman -S mesa nvidia(-lts) nvidia-settings nvidia-dkms nvidia-utils nvidia-prime（nvidia显卡驱动，用nvidia显卡就装，否则不用装）
   pacman -S xf86-video-amdgpu (AMD显卡驱动，用amd显卡的就装)
   ```

3.  声卡驱动

```text
pacman -S pipewire (alsa-utils) pipewire-pulse pipewire-jack pipewire-alsa
```

## 创建用户

```
useradd -m -G wheel -s /bin/bash username   
passwd username
```

