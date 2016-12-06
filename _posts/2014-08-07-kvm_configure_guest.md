---
layout: post
title: "KVM批量配置客户机"
description: ""
category: 虚拟化技术
tags: [kvm,libvirt,python]
---

在批量部署好虚拟机之后，需要对这些虚拟机进行配置，修改虚拟机的IP，DNS等。

在linux中，这些配置都是以文件的方式存在于磁盘之中，所有这里直接对虚拟机的磁盘镜像进行操作。

## 安装libguestds-tool
在这里会用到`libguestfs-tools`工具，先安装`apt-get instal libguestfs-tools`。

安装完成之后,会安装很多virt-开头的命令。

## 命令介绍

1. virt-cat 

    直接查看虚拟机里面的文件，类似于`cat`命令。

2. virt-edit

    直接编辑虚拟机里面的文件，类似于`vi/vim`。PS:这里虚拟机必须处于关机状态

3. virt-df

    直接查看虚拟机里面的磁盘使用情况，类似`df`命令。

4. virt-copy-out

    直接复制虚拟机里面的磁盘文件到本地磁盘上，类似于`cp`。

5. virt-copy-in

    直接复制本地磁盘文件到虚拟机磁盘上，类似于`cp`。

## 使用

比如对一台部署好的虚拟机进行配置，当然一种是登陆到虚拟机的系统里面，另一种可以在本地准备好配置文件，用`virt-copy-in`复制到虚拟机磁盘里面。

###准备配置文件模板

hostname文件模板:

    VM_NAME_GOES_HERE

hosts文件模板：

    127.0.0.1	localhost
    127.0.1.1	VM_NAME_GOES_HERE
    
    # The following lines are desirable for IPv6 capable hosts
    ::1     ip6-localhost ip6-loopback
    fe00::0 ip6-localnet
    ff00::0 ip6-mcastprefix
    ff02::1 ip6-allnodes
    ff02::2 ip6-allrouters

interface文件模板：

    #This file describes the network interfaces available on your system
    # and how to activate them. For more information, see interfaces(5).
    #
    # The loopback network interface
    auto lo
    iface lo inet loopback
    
    auto eth0
    iface eth0 inet static
    address IP_ADDRESS_GOES_HERE
    netmask 255.255.255.0
    gateway 192.168.1.1

只需要替换文件里面`VM_NAME_GOES_HERE`为虚拟机名，`IP_ADDRESS_GOES_HERE`为分配的IP地址即可。

###脚本文件
需要复制的配置文件保存在guestconf文件夹下，每台虚拟机一个子文件夹。

ips.txt文件内容：

    vm110,192.168.1.110,00:16:3e:43:c6:a4,5900,1024,1
    vm111,192.168.1.111,00:16:3e:f7:c1:2d,5901,1024,1
    vm112,192.168.1.112,00:16:3e:e2:51:24,5902,1024,1
    vm113,192.168.1.113,00:16:3e:7b:04:cb,5903,1024,1
    vm114,192.168.1.114,00:16:3e:de:e3:dd,5904,1024,1
    vm115,192.168.1.115,00:16:3e:a2:48:d5,5905,1024,1
    vm116,192.168.1.116,00:16:3e:6d:13:04,5906,1024,1
    vm117,192.168.1.117,00:16:3e:fb:c3:1a,5907,1024,1
    vm118,192.168.1.118,00:16:3e:70:f0:54,5908,1024,1

configureGuest.py脚本内容：

    #!/usr/bin/env python
    # _*_ coding:utf-8 _*_
    
    import sys,os
    import re
    from virtinst.util import *
    
    vm_file = './ips.txt'
    interface_file = '../template/interfaces'
    hosts_file = '../template/hosts'
    hostname_file = '../template/hostname'
    
    IP = 'IP_ADDRESS_GOES_HERE'
    VM_NAME = 'VM_NAME_GOES_HERE'
    
    guestconf_dir = '../guestconf/'
    
    f = open(vm_file)
    vm_config = f.readlines()
    f.close()
    
    #使用os模块进行文本替换  
    def context_replace(file, search_for, replace_with, new_file = 'new_file'):  
        #open files  
        fi = open(file)  
        fo = open(new_file, 'w')  
          
        #replace context  
        for line in fi.readlines():
            fo.write(line.replace(search_for, replace_with))  
          
        #close files  
        fi.close()  
        fo.close()  
    
    vms = [] # 虚拟机列表
    
    def make_file():
        for line in vm_config:
            # 匹配空白行
            if re.search('^\s$', line) != None:
                continue
            # 匹配注释
            passline = re.compile("#.*")
            if re.search(passline, line) != None:
                continue
            (vm_name, ip, mac_address, vnc_port, vm_mem, vm_vcpu)=line.strip().split(",")
            vms.append(vm_name)
            print vm_name, ip
            # 创建文件夹
            path = guestconf_dir + vm_name
            if not os.path.isdir(path):
                os.makedirs(path)
            outfile = path+'/hostname'
            context_replace(hostname_file, VM_NAME, vm_name, outfile)
            outfile = path +'/interfaces'
            context_replace(interface_file, IP, ip, outfile)
            outfile = path +'/hosts'
            context_replace(hosts_file, VM_NAME, vm_name, outfile)
    
    url = "qemu:///system"
    def startVM(vm_name):
        try:
            conn = libvirt.open(url)
        except Exception,e:
            print 'Faild to open connection to the hypervisor'
            sys.exit(1)
        vmname = conn.lookupByName(vm_name)
        try:
            vmname.create()
        except Exception,e:
            print "Failed to create %s:%s" %(vm_name,e)
            sys.exit(1)
        try:
            print "Domain 0:id %d running %s" %(vmname.ID(),vmname.name())
        except Exception,e:
            print e
        try:
            conn.close()
        except:
            print "Faild to close the connection!"
            sys.exit(1)
        print vm_name, "Start!"
    
    def upload():
        for vm in vms:
            print 'Configure', vm
            h_file = guestconf_dir + vm + '/interfaces'
            cmd = ['virt-copy-in', '-d', vm, h_file, '/etc/network']
            cmd = ' '.join(cmd)
            os.system(cmd)
            h_file = guestconf_dir + vm + '/hostname'
            cmd = ['virt-copy-in', '-d', vm, h_file, '/etc']
            cmd = ' '.join(cmd)
            os.system(cmd)
            h_file = guestconf_dir + vm + '/hosts'
            cmd = ['virt-copy-in', '-d', vm, h_file, '/etc']
            cmd = ' '.join(cmd)
            os.system(cmd)
            print vm, "Upload Done!"
    	startVM(vm)
            
    make_file()
    upload()

