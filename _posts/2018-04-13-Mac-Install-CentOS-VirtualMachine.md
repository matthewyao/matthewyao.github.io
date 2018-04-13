---
layout:     post
title:      MAC下安装CentOS虚拟机
discription: 本文描述了如何在MAC通过VirtualBox安装多台CentOS虚拟机。
date:       2018-04-13 18:05:00
catalog:    true
tags:       [MAC, 虚拟机, CentOS, VirtualBox, Hadoop, 大数据 ]
---

# CentOS安装
## 首先下载所有需要的安装包
首先需要在mac安装virtualbox和扩展包，下载地址：https://www.virtualbox.org/wiki/Downloads



下载CentOS7镜像：http://isoredirect.centos.org/centos/7/isos/x86_64/CentOS-7-x86_64-DVD-1708.iso

为共享文件夹做准备：http://download.virtualbox.org/virtualbox/5.2.8/VBoxGuestAdditions_5.2.8.iso

JDK for linux下载：http://download.oracle.com/otn-pub/java/jdk/8u161-b12/2f38c3b165be4555a1fa6e98c45e0808/jdk-8u161-linux-x64.tar.gz

## CentOS安装
安装教程参考：https://www.jianshu.com/p/eb0f3a3aed6d


# CentOS配置
## 网络配置
### 配置成静态IP模式
需要修改网卡的配置

vi /etc/sysconfig/network-scripts/ifcfg-enp-0s3
有可能是ifcfg-*（本机为ifcfg-enp-0s3）

修改这两项

BOOTPROTO=static    
ONBOOT=yes
新增这三项

IPADDR=172.22.80.xxx    //设置成自己本机的ip 
NETMASK=255.255.248.0 
GATEWAY=172.22.80.1
### DNS配置
DNS 官方建议在 /etc/sysconfig/network 中配置，比较简单直接给出配置

[root@node1 ~]# cat /etc/sysconfig/network

#Created by anaconda 
DNS1=10.128.16.28 
DNS2=8.8.8.8 
### 修改网络连接方式
将虚拟机网络改为桥接模式，具体如下图



重启网络 service network restart  

测试网络 ping www.baidu.com，若网络不通检查上述网络配置

### 安装工具包
更新yum：yum update

安装网络工具包：yum install net-tools


### host设置
#### 虚拟机设置host
设置主机名称： vi /etc/hostname     把里面的内容改为 node1 

#### 设置虚拟机主机名和IP映射
在虚拟机用  ifconfig 查看IP如（我的ip是这个）：172.22.80.172

vi /etc/hosts  添加  172.22.80.172 node1

#### 本机设置host
sudo vi /etc/hosts   添加 172.22.80.172 node1

在本机ping node1测试，若ping不通，请检查host配置和网络配置

## 共享文件设置
在虚拟机执行：yum install gcc gcc-c++ make kernel-headers kernel-devel bzip2 wget

关闭虚拟机后，将下载的GuestAdditions挂载到存储上，虚拟机开机



### 挂载
新增目录  

mkdir /mnt/share
挂载到CD/DVD虚拟光驱

mount -t auto /dev/cdrom  /mnt/share  
可能有报错信息，没关系，接着往下

重新回到 /mnt/share  看不是挂载成功，若挂载成功，执行脚本

sh ./VBoxLinuxAdditions.run
### 设置共享




实现 开机自动挂载

vi /etc/bashrc
在最后添加一行

mount -t vboxsf share /mnt/share
reboot  重启 查看挂载是否成功

## JDK安装
### JDK下载
把下载JDK安装包放到共享文件下，然后复制到 /usr/local/src解压

tar -zxvf jdk-8u161-linux-x64.tar.gz
### JDK配置
新建文件java.sh  放到共享文件夹下，配置内容

export JAVA_HOME=/usr/local/src/jdk1.8.0_161 
export CLASSPATH=.:$JAVA_HOME/jre/lib/rt.jar:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar 
export PATH=$PATH:$JAVA_HOME/bin
复制到 /etc/profile.d/java.sh

执行 source /etc/profile  

java -version查看jdk  是否安装完毕

## SSH设置
### 免密登录
vim /etc/ssh/sshd_config 添加 PermitRootLogin yes

service sshd restart

首先查看本地是否已经生成过秘钥：



如没有，则生成公钥和私钥：

ssh-keygen -t rsa -P '' -f ~/.ssh/id_rsa
然后将公钥复制到共享文件夹：

cp /Users/yaokuan/.ssh/id_rsa.pub /Users/yaokuan/Documents/share
### 虚拟机配置
登陆node1，查看是否已经存在~/.ssh/authorized_keys

若没有.ssh文件夹则新增 mkdir .ssh

若没有authorized_keys文件则新建 touch  ~/.ssh/authorized_keys

cat /mnt/share/id_rsa.pub >> ~/.ssh/authorized_keys
然后尝试免密登陆



免密码登录成功，到此，node1即安装成功

## 关闭防火墙
systemctl stop firewalld.service 
systemctl disable firewalld.service 
systemctl status firewalld.service
# 虚拟机复制
## 复制
我们以node1为模板复制node2，在virtualbox中选择node1右键 -> 复制



改名并勾上初始化mac地址



选择完全复制



## 修改配置
复制完成后登陆（和node1的root账号密码一致），然后修改hostname

通过桥接模式上网的虚拟机在复制之后，出现三台机器的ip地址都是一样的，还都可以上网，因为之前复制虚拟机有勾选重新初始化mac地址，

所以现在只需要启动虚拟机，修改/etc/sysconfig/network-scripts/ifcfg-enp0s3，将IPADDR改为不同node1的ip

IPADDR=172.22.80.173
最后重启网络：service network restart

即可使用不同的ip，然后依次复制node3和node4

## 修改hosts
当四台虚拟机都配置完成后，我们修改/etc/hosts文件，加入node1~4的IP（使用你的虚拟机IP）映射，使节点互通

172.22.80.172 node1
172.22.80.173 node2
172.22.80.174 node3
172.22.80.175 node4
# 设置虚拟机之前免密登录
因为后续虚拟机各节点中需要通过ssh互相登录，每次都输入密码是不太现实的，所以需要实现4台虚拟机之前的免密登录，下面以node1为例

## 生成公钥
首先在node1上生成公钥：ssh-keygen -t rsa



一路回车，都设置为默认值，然后在当前用户的Home目录下的.ssh目录中会生成公钥文件（id_rsa.pub）和私钥文件（id_rsa）

id_rsa.pub为私钥，id_rsa为公钥

## 分发公钥
然后将node1生成的公钥分发到node2~4，下面以node2为例：ssh-copy-id node2



这个时候就可以在node2的.ssh目录中的authorized_keys中看到node1的公钥了，然后node1就可以免密登录node2了



然后同样地在node2~4上重复4.1和4.2的步骤

大功告成！
