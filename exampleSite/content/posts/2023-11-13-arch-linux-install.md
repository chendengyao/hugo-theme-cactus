---
title: Arch Linux安装
date: 2023-11-19 15:10:18
categories: blog
tags: 
- Arch Linux
- linux
keywords: Arch
---

# Arch Linux 安装

因为安装了 Arch Linux 好几次了，但是每次安装还是得到处找资料，这里记录一下安装过程，便于以后查找。

## 制作镜像
1. 下载镜像，直接到 Arch Linux 官方网站进行下载 https://archlinux.org/download/
2. 刻录镜像到U盘，使用 [rfrus](https://rufus.ie/zh/) 或者 [balenaEtcher](https://etcher.balena.io/) 都可以，我选择的是 balenaEtcher，傻瓜式操作。

## 系统安装

1. 使用 UEFI 启动U盘

   惠普启动使用 F9 可进入启动选项菜单，选择插入的U盘即可。

2. 连接网络

    - 若使用的是无线Wifi，使用 iwctl:

      ```bash
      # 查看网卡号
      device list
      # 扫描该网口
      station wlan0 scan
      # 查看扫描到的wifi
      station wlan0 get-networks
      # 连接wifi
      station wlan0 connect (wifi名称)
      ```

    - 若使用的是有线

      可直接联网，无需设置

   ` ping www.baidu.com` 验证联网

   若联网成功，此时若有其他电脑，建议在其他电脑连接进行使用

3. 更新时钟

   ``` timedatectl ```

4. 磁盘分区

   ```bash
   fdisk -l # 查看硬盘
   cfdisk /dev/the_disk_to_be_partitioned # 进入要分区的硬盘
   ```
   进入图形化分区，先删除所有分区，分区大小：

   | 分区     |   挂载点   | 表头     |
      | -------- | :------: | -------- |
   | /dev/sda1 | /boot/efi | 1G |
   | /dev/sda2 | /home     | 100G (看个人文件大小) |
   | /dev/sda3 | swap      | 16G (建议根据大家自己内存大小) |
   | /dev/sda4 | /         | 剩下全部 |

   接着进行分区格式化和初始化
   ```bash
    mkfs.fat -F32 /dev/sda1 # boot分区必须使用fat32格式
   
    mkfs.ext4 /dev/sda2 # / 和 /home 一般使用ext4分区格式
   
    mkswap /dev/sda3 # swap分区使用swap格式
   
    swapon /dev/sda3 # 激活swap分区，不激活不会使用这部分空间
   
    mkfs.ext4 /dev/sda4 # /home 分区
   ```

   分区完成建议使用 `lsblk`查看是否分区正确，若没有错误，则进行下一步挂载

   ```bash
   mkdir -p /mnt/boot/efi    (在新系统的 / 目录中新建 efi 文件夹，-p 参数表示递归创建)
   mount /dev/sda1 /mnt/boot/efi    (将 boot 分区挂载到efi文件夹)
   mkdir /mnt/home    (同理，创建home目录)
   mount /dev/sda2 /mnt/home    (将home分区挂载到home目录)
   mount /dev/sda4 /mnt    (将 / 分区挂载到 live系统中的 /mnt 目录，此时 /mnt 目录就是我们所安装系统的 / 目录)
   ```

5. 安装必要的包

   换源：很重要，不然等到天荒地老。方法很多，可以直接修改/etc/pacman.d/mirrorlist文件，也可以直接 ` reflector --country China --age 72 --sort rate --protocol https --save /etc/pacman.d/mirrorlist `

   执行安装：

   ` pacstrap /mnt base linux linux-headers linux-firmware base-devel `

6. 写入分区表

   ` genfstab -U /mnt >> /mnt/etc/fstab `

7. 进入新系统

   ` arch-chroot /mnt `

## 配置系统

1. 安装软件

   ` pacman -S neovim iwd ttf-dejavu sudo bluez usbmuxd networkmanager dhcpcd wqy-zenhei ntfs-3g `

2. 设置时区和时间

   ```
   ln -sf /usr/share/zoneinfo/Asia/Shanghai /etc/localtime    (设置时区为上海)
   hwclock --systohc
   ```

3. 设置语言

   输入“vim /etc/locale.gen”，删除【en_US.UTF-8】和【zh_CN.UTF-8】两项前面的“#”，保存。（如果有其他语言需求也可以自行取消注释，比如台湾地区和港澳地区）

   输入“locale-gen”，再输入

   ```
   echo LANG=en_US.UTF-8 >> /etc/locale.conf
   ```

4. 设置主机名

   ` echo hostname >> /etc/hostname `

5. 设置root密码

   ` passwd`

    6. 设置网络

   ` vim /etc/hosts`

    7. 安装引导

    ```bash
    pacman -S grub efibootmgr
    grub-install /dev/sda
    grub-mkconfig -o /boot/grub/grub.cfg
    ```

8. 退出并重启

   ```bash
   exit
   umount -R /mnt
   reboot
   ```