# 2. 网络配置

## 2.1 手动配置静态 IP

### 2.1.1 查看宿主机-虚拟机 NAT 网关

```bash
# 宿主机是 macOS，虚拟机软件为 VMware Fusion
$ cat /Library/Preferences/VMware\ Fusion/vmnet8/nat.conf

# VMware NAT configuration file
# Manual editing of this file is not recommended. Using UI is preferred.

[host]

# NAT gateway address
ip = 192.168.242.2      # 不同电脑不一样
netmask = 255.255.255.0

# VMnet device if not specified on command line
device = vmnet8
...
```

为服务器配置静态 IP 时将会用到网关 IP `192.168.242.2` 。

### 2.1.2 设置服务器静态 IPv4

查看网络情况：

```bash
$ ip addr

1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host
       valid_lft forever preferred_lft forever
2: ens33: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 00:0c:29:0d:1b:f3 brd ff:ff:ff:ff:ff:ff
    inet 192.168.242.162/24 brd 192.168.242.255 scope global dynamic ens33 # dynamic
       valid_lft 1007sec preferred_lft 1007sec
    inet6 fe80::20c:29ff:fe0d:1bf3/64 scope link
       valid_lft forever preferred_lft forever
```

Ubuntu Server 20.04 的网络配置工具是 `netplan`。

```bash
$ cat /etc/netplan/00-installer-config.yaml

# This is the network config written by 'subiquity'
network:
  ethernets:
    ens33:
      dhcp4: true
  version: 2
```

可以看出，原来的网络配置中，网卡 ens33 开启了 DHCP。备份好这份配置文件之后，将其内容修改为：

```yaml
# This is the network config written by 'subiquity'
network:
  ethernets:
    ens33:
      addresses: [192.168.242.162/24]
      gateway4: 192.168.242.2   # 宿主机-虚拟机网关
      nameservers:
        addresses: [4.2.2.2, 8.8.8.8]
  version: 2
```

保存好配置后，执行 `sudo netplan apply`。

```bash
$ ip addr

1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host
       valid_lft forever preferred_lft forever
2: ens33: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 00:0c:29:0d:1b:f3 brd ff:ff:ff:ff:ff:ff
    inet 192.168.242.162/24 brd 192.168.242.255 scope global ens33 # 不再是 dynamic
       valid_lft forever preferred_lft forever
    inet6 fe80::20c:29ff:fe0d:1bf3/64 scope link
       valid_lft forever preferred_lft forever
```

记得 ping 一下外网，看网络是否能正常工作。

```bash
$ ping 114.114.114.114
PING 114.114.114.114 (114.114.114.114) 56(84) bytes of data.
64 bytes from 114.114.114.114: icmp_seq=1 ttl=128 time=32.5 ms
64 bytes from 114.114.114.114: icmp_seq=2 ttl=128 time=33.1 ms
^C
--- 114.114.114.114 ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 1001ms
rtt min/avg/max/mdev = 32.525/32.818/33.111/0.293 ms
```

至此，为服务器手动配置静态 IP 已完成。

## 2.2 使用 Ansible 配置静态 IP

## 2.3 保存快照

完成以上静态 IP 配置后，关闭虚拟机并保存当前快照，这份快照将在后面派上用场。

---

下一节，将介绍 Ansible Playbook 的典型目录结构。
