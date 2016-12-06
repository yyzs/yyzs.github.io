---
layout: post
title: "使用libvirt管理kvm虚拟机"
description: ""
category: 虚拟化技术
tags: [kvm,libvirt]
---

## 什么是libvirt 

Libvirt是管理虚拟机和其他虚拟化功能，比如存储管理，网络管理的软件集合。它包括一个API库，一个守护程序（libvirtd）和一个命令行工具（virsh）；libvirt本身构建于一种抽象的概念之上。它为受支持的虚拟机监控程序实现的常用功能提供通用的API。 

<!--more-->

## libvirt xml配置文件

    <domain type='kvm'> 
    <name>vm01</name> 
    <uuid>f5b8c05b-9c7a-3211-49b9-2bd635f7e2aa</uuid> 
    <memory>1048576</memory> 
    <currentMemory>1048576</currentMemory> 
    <vcpu>1</vcpu> 
    <os> 
        <type>hvm</type> 
        <boot dev='cdrom'/> 
    </os> 
    <features> 
        <acpi/> 
    </features> 
    <clock offset='utc'/> 
    <on_poweroff>destroy</on_poweroff> 
    <on_reboot>restart</on_reboot> 
    <on_crash>destroy</on_crash> 
    <devices> 
        <emulator>/usr/bin/kvm</emulator> 
        <disk type="file" device="disk"> 
            <driver name="qemu" type="raw"/> 
            <source file="/var/lib/libvirt/images/vm01.img"/> 
            <target dev="vda" bus="virtio"/> 
            <address type="pci" domain="0x0000" bus="0x00" slot="0x04" function="0x0"/> 
        </disk> 
        <disk type="file" device="cdrom"> 
            <driver name="qemu" type="raw"/> 
            <source file="/iso/ubuntu-13.10-server-amd64.iso"/> 
            <target dev="hdc" bus="ide"/> 
            <readonly/> 
            <address type="drive" controller="0" bus="1" target="0" unit="0"/> 
        </disk> 
        <controller type="ide" index="0"> 
            <address type="pci" domain="0x0000" bus="0x00" slot="0x01" function="0x1"/> 
        </controller> 
        <interface type='bridge'> 
            <mac address='52:54:aa:00:f0:51'/> 
            <source bridge='br0'/> 
        </interface> 
        <input type='mouse' bus='ps2'/> 
        <graphics type='vnc' port='-1' autoport="yes" listen='127.0.0.1'/> 
    </devices> 
    </domain> 

## 虚拟机管理

### 查看虚拟机 查看本机连接的虚拟机 

    virsh list 默认只列出活动中得虚拟机，加上

`--all`参数，查看所有虚拟机 
    virsh list --all

### 定义、启动、创建虚拟机

1.  定义虚拟机 
         virsh define vm01.xml 只是定义了，还未启动虚拟机vm01。

2.  启动虚拟机 
         virsh start vm01

3.  创建虚拟机 
         virsh create vm01.xml 创建虚拟机的作用相当于定义+启动虚拟机。

### 关闭、销毁、取消定义虚拟机

1.  关闭虚拟机 
         virsh shutdown vm01 与启动虚拟机相对。

2.  销毁虚拟机 
         virsh destory vm01 销毁vm01，取消vm01的定义。此步骤之后无法执行取消定义虚拟机。

3.  取消定义虚拟机 
         virsh undefine vm01 与
    
    `virsh define vm01`相对。

### 其他virsh命令

1.  编辑kvm虚拟机配置文件 
         virsh edit vm01.xml
    
    `virsh edit`会调用vi/vim编辑vm01.xml配置文件。**不推荐直接用vi/vim直接编辑。**通过vi/vim直接编辑之后，要重新定义虚拟机。
2.  开机启动虚拟机 
         virsh autostart vm01 在
    
    `/etc/libvirt/qemu/autostart`里面有开机启动的虚拟机xml链接。
3.  导出虚拟机配置文件 
         virsh dumpml vm01 > vm02.xml

4.  挂起虚拟机 
         virsh suspend vm01

5.  恢复虚拟机 
         virsh resume vm01

### 虚拟机的克隆 

kvm虚拟机克隆可以分为两种，一种是直接直接虚拟机克隆，另一种是通过导出配置文件来克隆。 

#### 本机虚拟机直接克隆

    virt-clone -o vm1 -n vm2 -f /var/lib/libvirt/images/vm2.img 说明：以vm1为源，克隆vm1虚拟机，并创建名为vm2的虚拟机，使用磁盘文件vm2.img。 启动虚拟机vm2，配置主机名，IP。 

#### 复制配置文件和磁盘文件克隆

1.  导出vm1虚拟机的配置文件。 
         virsh dumpml vm01 > vm02.xml

2.  复制虚拟磁盘文件 
         cp vm1.img vm3.img

3.  直接编辑vm02.xml，修改name、uuid、disk位置、vnc端口。
4.  定义新虚拟机vm02，并启动。 
         virsh define vm02.xml
         virsh start vm02

5.  登入虚拟机，修改主机名、IP等。 

参考：[kvm虚拟机克隆][1] 

## 存储管理

### 创建镜像 创建一个大小为10G的磁盘镜像 

    qemu-img create -f raw vm01.img 10G

`-f`指定磁盘镜像格式，kvm默认为raw，也可以用支持动态扩展的qcow2格式。 
### 查看镜像 查看镜像文件信息 

    qemu-img info vm01.img 如果文件是使用稀疏文件的存储方式，也会显示出它的本来分配的大小以及实际已占用的磁盘空间大小 

### 转换磁盘格式

    qemu-img convert-f raw -O qcow2 vm1.img vm2.img

*   -f 源镜像格式
*   -O 目标镜像格式

 [1]: http://koumm.blog.51cto.com/703525/1291793