---
layout: post
title: "在Ubuntu12.04上安装KVM虚拟化软件"
description: ""
category: 虚拟化技术
tags: [kvm,libvirt,ubuntu]
---

KVM是 Kernel-based Virtual Machine 的简称，是一个开源的系统虚拟化模块。是一种基于内核的虚拟机软件。 
## 证实主机支持硬件虚拟化

验证主机的处理器是否具备硬件虚拟化的条件（比如英特尔VT或AMD-V） 运行下面的命令 

    egrep '(vmx|svm)' --color /proc/cpuinfo  如果输出中不含

`vmx`或`svm`标记，这表示主机的处理器没有支持硬件虚拟化的功能。证实主机处理器有`vmx`或`svm`后，就可以安装kvm了。 

## 安装KVM 安装kvm及相关管理工具 

    apt-get install qemu-kvm libvirt-bin 测试下是否安装成功 

    virsh --connect qemu:///system list 得到下面结果 

    Id Name                 State
    ----------------------------------

## 配置桥接网络

下面需要在服务器上建立网桥，以便可以从其他主机访问建立的虚拟机，就好像虚拟机是网络中得物理系统。 

### 安装必要的程序包

    apt-get install bridge-utils

### 创建Linux网桥

    brctl addbr br0 到这里就创建了br0网桥。 

### 配置网桥 要想使用

`/etc/network/interfaces`里面的配置来设定网络，首先要禁用系统上的Network Manager(网络管理器)。 
    sudo stop network-manager
    $ echo "manual" | sudo tee /etc/init/network-manager.override 禁用网络管理器之后，在

`/etc/network/interfaces`中配置Linux网桥br0。 
    auto lo 
    iface lo inet loopack
    
    auto br0 
    iface br0 inet dhcp 
    bridge_ports eth0 
    bridge_stp off 
    bridge_fd 0 
    bridge_maxwait 0  这里假设，eth0是主要的网络接口，可以访问外部网络。eth0通过DHCP获得其IP地址。 注意：

`/etc/network/interface`中没有etho的配置。etho网桥受制于br0时，Linux网桥br0接过eth0的配置。 在笔记本上测试的时候，发现用无线网卡wlan0是不能用于网桥的，必须用有线连接，原因目前我还为得知。 重启网络服务，证实Linux网桥已成功配置。 
    /etc/init.d/networking restart 
    ifconfig 如果配置成功，br0应该被赋予eth0的DHCP IP地址，eth0应该没有被赋予任何IP地址。 

    br0       Link encap:Ethernet  HWaddr b8:88:e3:3b:ba:39  
              inet addr:192.168.1.110  Bcast:192.168.1.255  Mask:255.255.255.0
              inet6 addr: fe80::ba88:e3ff:fe3b:ba39/64 Scope:Link
              UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
              RX packets:1664 errors:0 dropped:0 overruns:0 frame:0
              TX packets:700 errors:0 dropped:0 overruns:0 carrier:0
              collisions:0 txqueuelen:0 
              RX bytes:291368 (291.3 KB)  TX bytes:82245 (82.2 KB)
    
    eth0      Link encap:Ethernet  HWaddr b8:88:e3:3b:ba:39  
              UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
              RX packets:1712 errors:0 dropped:0 overruns:0 frame:0
              TX packets:828 errors:0 dropped:0 overruns:0 carrier:0
              collisions:0 txqueuelen:1000 
              RX bytes:317423 (317.4 KB)  TX bytes:99233 (99.2 KB)
    
    lo        Link encap:Local Loopback  
              inet addr:127.0.0.1  Mask:255.0.0.0
              inet6 addr: ::1/128 Scope:Host
              UP LOOPBACK RUNNING  MTU:65536  Metric:1
              RX packets:1571 errors:0 dropped:0 overruns:0 frame:0
              TX packets:1571 errors:0 dropped:0 overruns:0 carrier:0
              collisions:0 txqueuelen:0 
              RX bytes:243835 (243.8 KB)  TX bytes:243835 (243.8 KB)
    
    virbr0    Link encap:Ethernet  HWaddr d6:1e:18:04:a2:b0  
              inet addr:192.168.122.1  Bcast:192.168.122.255  Mask:255.255.255.0
              UP BROADCAST MULTICAST  MTU:1500  Metric:1
              RX packets:0 errors:0 dropped:0 overruns:0 frame:0
              TX packets:0 errors:0 dropped:0 overruns:0 carrier:0
              collisions:0 txqueuelen:0 
              RX bytes:0 (0.0 B)  TX bytes:0 (0.0 B)

## 创建虚拟机

### 从命令行创建虚拟机

虚拟机的配置文件存储在一个域XML文件中。所以创建虚拟机之前就需要准备好这个域XML文件。 下面是一个虚拟机的域XML文件示例。按照自己的需求在这个文件上定制自己的XML文件。 

    <domain type='kvm'>
      <name>vm01</name>
      <uuid>11f398d5-8c67-e650-4cbb-2c789f6d2406</uuid>
      <memory>1048576</memory>
      <currentMemory>1048576</currentMemory>
      <vcpu>1</vcpu>
      <os>
        <type arch='x86_64' machine='pc-1.0'>hvm</type>
        <boot dev='cdrom'/>
      </os>
      <features>
        <acpi/>
        <apic/>
        <pae/>
      </features>
      <clock offset='utc'/>
      <on_poweroff>destroy</on_poweroff>
      <on_reboot>restart</on_reboot>
      <on_crash>restart</on_crash>
      <devices>
        <emulator>/usr/bin/kvm</emulator>
        <disk type='file' device='disk'>
          <driver name='qemu' type='raw'/>
          <source file='/var/lib/libvirt/images/vm01.img'/>
          <target dev='vda' bus='virtio'/>
          <address type='pci' domain='0x0000' bus='0x00' slot='0x05' function='0x0'/>
        </disk>
        <disk type='file' device='cdrom'>
          <driver name='qemu' type='raw'/>
          <source file='/iso/ubuntu.iso'/>
          <target dev='hdc' bus='ide'/>
          <readonly/>
          <address type='drive' controller='0' bus='1' unit='0'/>
        </disk>
        <controller type='ide' index='0'>
          <address type='pci' domain='0x0000' bus='0x00' slot='0x01' function='0x1'/>
        </controller>
        <interface type='bridge'>
          <mac address='52:54:00:f5:c5:4b'/>
          <source bridge='br0'/>
          <model type='virtio'/>
          <address type='pci' domain='0x0000' bus='0x00' slot='0x03' function='0x0'/>
        </interface>
        <serial type='pty'>
          <target port='0'/>
        </serial>
        <console type='pty'>
          <target type='serial' port='0'/>
        </console>
        <input type='mouse' bus='ps2'/>
        <graphics type='vnc' port='-1' autoport='yes' listen='0.0.0.0'>
          <listen type='address' address='0.0.0.0'/>
        </graphics>
        <sound model='ich6'>
          <address type='pci' domain='0x0000' bus='0x00' slot='0x04' function='0x0'/>
        </sound>
        <video>
          <model type='cirrus' vram='9216' heads='1'/>
          <address type='pci' domain='0x0000' bus='0x00' slot='0x02' function='0x0'/>
        </video>
        <memballoon model='virtio'>
          <address type='pci' domain='0x0000' bus='0x00' slot='0x06' function='0x0'/>
        </memballoon>
      </devices>
    </domain> 上面这个域XML文件定义了下面这个虚拟机。 1GB内存、一个虚拟处理器和一只硬盘。 磁盘映像：/var/lib/libvirt/images/vm01.img 从光盘驱动器启动（/iso/ubuntu.iso）。 网络：通过br0实现桥接网络。 里面的UUID字符串可以随机生成。想获得一个随机性的UUID，你可以使用uuid命令行工具。 

    apt-get install uuid
    uuid

#### 创建虚拟机的初始化磁盘映像

    qemu-img create -f qcow2 /var/lib/libvirt/images/vm01.img 10G 使用qcow2作为一种磁盘映像格式的优点在于，磁盘映像起初并不是以最大值（10GB）的形式创建，不过随着磁盘被批量装入数据，可以慢慢变大。 

#### 启动虚拟机

    $ virsh --connect qemu:///system create alice.xml 
    Domain alice created from alice.xml  执行下面命令，查看启动的虚拟机 

    $ virsh --connect qemu:///system list 
    Id    Name                           State 
    -------------------------------------------------------------- 
    3     alice                          running  查看该虚拟机（比如vnet0）的虚拟接口已成功添加到之前创建的Linux网桥br0。 

    $ brctl show

### 通过virt-manager创建虚拟机 

安装虚拟机virtual machine manager 

    sudo apt-get install virt-manager virt-viewer 图形界面安装很简单，这里就不多介绍了。 

## KVM虚拟机的基本管理

1.  查看虚拟机的状态 
        virsh list 默认只显示正在运行当中的虚拟机，加上
    
    `--all`可以显示所有虚拟机。
2.  启动KVM虚拟机 
        virsh start vm01

3.  关闭KVM虚拟机 
        virsh shutdown vm01 结语：到此，基本上KVM虚拟机已经能使用了。
