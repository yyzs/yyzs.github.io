---
layout: post
title: "KVM批量创建删除虚拟机"
description: ""
category: 虚拟化技术
tags: [kvm,libvirt,python]
---

如果只是创建一两个虚拟机，直接敲几行命令就完事了，但如果要部署20+以上的虚拟机，我想重复地敲同样地命令会敲疯了（同样的事让我做第二遍，那就交给计算机来吧）。 libvirt不仅提供了一个守护程序libvirtd，一个工具virsh，还提供了一个API库。这个API库支持多种语言的绑定，其中就包括了python。更多关于libvirt的API库的信息，可以查阅[官方文档][1]。 要批量创建虚拟机，首先要有一个模板系统，这个就不多说了，最主要的是要得到该系统的xml配置文件和系统的磁盘镜像文件。 <!--more--> 对于想要创建的虚拟机，只需要修改下配置文件ips.txt即可。 ips.txt的内容为： 

    vm110,192.168.1.110,00:16:3e:43:c6:a4,5900,1024,1
    vm111,192.168.1.111,00:16:3e:f7:c1:2d,5901,1024,1
    vm112,192.168.1.112,00:16:3e:e2:51:24,5902,1024,1
    vm113,192.168.1.113,00:16:3e:7b:04:cb,5903,1024,1
    vm114,192.168.1.114,00:16:3e:de:e3:dd,5904,1024,1
    vm115,192.168.1.115,00:16:3e:a2:48:d5,5905,1024,1
    vm116,192.168.1.116,00:16:3e:6d:13:04,5906,1024,1
    vm117,192.168.1.117,00:16:3e:fb:c3:1a,5907,1024,1
    vm118,192.168.1.118,00:16:3e:70:f0:54,5908,1024,1
    vm119,192.168.1.119,00:16:3e:d1:92:e3,5909,1024,1
     从左往右分别是虚拟机名字，虚拟机IP地址，虚拟机MAC地址，虚拟机的VNC端口，虚拟机内存大小（M），虚拟机vcpu。 只需要执行以下命令 

    python createVM.py --action=add --srcxml=/etc/libvirt/qemu/model.xml --srcimg=/var/lib/libvirt/images/model.img
     即可根据ips.txt里面的内容创建出虚拟机来。 执行

`python createVM.py --action=delete`可根据ips.txt内容删除虚拟机。 createVM.py文件内容: 
    #!/usr/bin/env python
    # _*_ coding:utf-8 _*_
    
    import os, sys
    import libvirt
    import re
    import shutil
    from virtinst.util import *
    if sys.version_info < (2.5):
        import xml.etree as ET
    else:
        try:
            import xml.etree.cElementTree as ET
        except ImportError:
            import xml.etree.ElementTree as ET
    from optparse import OptionParser
    
    
    #Define variables
    
    template_dir_path = ''
    template_img_path = template_dir_path + "/img"
    template_xml_path = template_dir_path + "/xml"
    vm_img_path = "/var/lib/libvirt/images"
    vm_xml_parh = "/etc/libvirt/qemu"
    vm_file = template_dir_path + "/conf/vm.ini"
    url = "qemu:///system"
    
    # Function
    def file_exists(file):
        '''
        Judge the file is exists
        Args:
            file:   the file
        '''
        if os.path.exists(file):
            return 1
        else:
            return 0
    
    def copy_vm_img_file(src_img_file, dst_img_file):
        '''
        Copy the img file.
    
        Args:
            src_img_file: the source img file
            dst_img_file: the target img file
        '''
        print "Start Copy %s to %s" %(src_img_file, dst_img_file)
        if file_exists(dst_img_file):
            print "File %s exists!" % dst_img_file
            sys.exit(1)
        shutil.copyfile(src_img_file,dst_img_file)
        print "Done!"
    
    def create_vm_incremental_mirris_file(src_img_file, dst_img_file):
        '''
        Create VM Incremental(增量) mirror
        Args:
            src_img_file: the source img file
            dst_img_file: the target incremental mirror
        '''
        print "Start Create %s" %(dst_img_file)
        if file_exists(dst_img_file):
            print "File %s exists!" % dst_img_file
            sys.exit(1)
        # 调用qemu-img create命令
        cmd = ['qemu-img','create','-b',basic_img_file, '-f','qcow2', dst_img_file]
        cmd = ' '.join(cmd)
        os.system(cmd)
        print "Done!"
    
    def create_vm_xml_file(src_xml_file, vm_name, dst_img_file, mac_address = '-1', vnc_port='5900', vm_mem = '1024', vm_vcpu = '1'):
        '''
        Create VM XML file.
    
        Args:
            src_xml_file:   the modle xml file
            vm_name:        the name of the VM
            dst_img_file:   the disk image file of the VM
            mac_address:    the MAC Address of the VM
            vnc_port:       the Port of VM's VNC e.g. '5900'
            vm_mem:         the memory of VM (unit:M), is a string e.g. '1024'
            vm_vcpu:        the vcpu of the VM, is a string e.g. '1'
            id_num:         the ID of VM
        '''
        config = ET.parse(src_xml_file)
        # id
        #num = config.find('domain')
        #num.attrib['id'] = id_num
        # name
        name = config.find('name')
        name.text = vm_name.strip()
        # uuid
        uuid = config.find('uuid')
        uuid.text = uuidToString(randomUUID())
        # mem
        mem = config.find('memory')
        memkb = str(int(1024)*int(vm_mem.strip()))
        mem.text = memkb
        currentMemory = config.find('currentMemory')
        currentMemory.text = memkb
        # vcpu
        vcpu = config.find('vcpu')
        vcpu.text = vm_vcpu.strip()
        # mac
        mac = config.find('devices/interface/mac')
        if mac_address == '-1':
            mac.attrib['address'] = randomMAC(type='qemu')
        else:
            mac.attrib['address'] = mac_address.strip() # 由外界的配置文件定义了mac address
        #disk
        disk = config.find('devices/disk/source')
        disk.attrib['file']=dst_img_file
        # vnc port
        port = config.find('devices/graphics')
        port.attrib['port'] = vnc_port
        if vnc_port == '-1':
            port.attrib['autoport'] = 'yes'
        else:
            port.attrib['autoport'] = 'no'
    
        vm_xml_name=vm_name.strip() + '.xml'
        vm_xml_file=os.path.join(vm_xml_path,vm_xml_name)
        if file_exists(vm_xml_file):
            print "File %s exists, abort" % vm_xml_file
            sys.exit(1)
        config.write(vm_xml_file)
        print "Created vm config file %s" % vm_xml_file
        #print "Use disk image %s, you must create it from the template disk: %s" % (disk_image, disk_old)
        print "Done!"
        # Start VM
        print "Start VM"
        start_vm(vm_xml_file,vm_name)
    
    def delete_file(file_name):
        if file_exists(file_name):
            #os.unlink(file_name)
            os.remove(file_name)
    
    def delete_vm(vm_name):
        vmimg=vm_name+".img"
        vmxml=vm_name+".xml"
        img_file=os.path.join(vm_img_path,vmimg)
        xml_file=os.path.join(vm_xml_path,vmxml)
        try:
            conn = libvirt.open(url)
        except Exception,e:
            print 'Faild to open connection to the hypervisor'
            sys.exit(1)
        try:
            server=conn.lookupByName(vm_name)
        except Exception,e:
            print e
            sys.exit(1)
        if server.isActive():
            print "VM %s will be shutdown!" %vm_name
            try:
                #server.shutdown()#VM need install acpid
                server.destroy()
            except Exception,e:
                print e
                sys.exit(1)
        print "VM %s will be delete!" %vm_name
        try:
            server.undefine()
        except Exception,e:
            print e
            sys.exit(1)
        delete_file(img_file)
        delete_file(xml_file)
        try:
            conn.close()
        except:
            print "Faild to close the connection!"
            sys.exit(1)
        print "Done"
        print "="*100
    
    def start_vm(vm_xml_file, vm_name):
        '''
        Create VM and start VM (include define VM and start VM)
        '''
        try:
            conn = libvirt.open(url)
        except Exception,e:
            print 'Faild to open connection to the hypervisor'
            sys.exit(1)
        create = True
        if create:
            xmlfile=open(vm_xml_file)
            xmldesc=xmlfile.read()
            xmlfile.close()
        try:
            vmname = conn.defineXML(xmldesc)            # define VM ,not start
        except Exception,e:
            print "Failed to define %s:%s" %(vm_name,e)
            sys.exit(1)
        if vmname is None:
            print 'whoops this shouldnt happen!'
        #try:
        #    vmname.create()
        #except Exception,e:
        #    print "Failed to create %s:%s" %(vm_name,e)
        #    sys.exit(1)
        try:
            print "Domain 0:id %d running %s" %(vmname.ID(),vmname.name())
        except Exception,e:
            print e
        try:
            conn.close()
        except:
            print "Faild to close the connection!"
            sys.exit(1)
        print "Done!"
        print "="*100
    
    def main():
        '''
        open config file
        '''
        f = open(vm_file)
        vm_config = f.readlines()
        f.close()
    
        parser = OptionParser()
        parser.add_option("--srcimg")
        parser.add_option("--srcxml")
        parser.add_option("--action")
        (options, args) = parser.parse_args()
    
        if options.srcimg:
            src_img_file = srcimg   # 源镜像文件
        if options.srcxml:
            src_xml_file = srcxml   # 模板xml文件
        if options.action:
            act = action            # 动作
    
        for line in vm_config:
            # 匹配空白行
            if re.search('^\s$', line) != None:
                continue
            # 匹配注释
            passline = re.compile("#.*")
            if re.search(passline, line) != None:
                continue
            (vm_name, ip, mac_address, vnc_port, vm_mem, vm_vcpu)=line.strip().split(",")
    
            if act == 'add':
                # Add VM
                dst_img_file=os.path.join(vm_img_path,vm_name.strip()+".img")           # 目标镜像路径
                if not (file_exists(src_img_file) and file_exists(src_xml_file)):
                    print "File %s or %s not exists,abort!" %(src_img_file,src_xml_file)
                    sys.exit(1)
                # Create VM img file
                print "Create incrememtal mirris file"
                create_vm_incremental_mirris_file(src_img_file, dst_img_file)
                # Create VM XML file
                print "Create VM Xml file"
                create_vm_xml_file(src_xml_file, vm_name, dst_img_file, mac_address, vnc_port, vm_mem, vm_vcpu)
            elif action == 'delete':
                # Delete vm
                print "Delete VM %s" % vm_name
                delete_vm(vm_name)

 [1]: http://libvirt.org/html/libvirt-libvirt.html