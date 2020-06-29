# 1. 环境准备

## 1.1 一台全新的 Linux 服务器

[ubuntu_mrs]: https://mirrors.tuna.tsinghua.edu.cn/ubuntu-releases/20.04/
[ubuntu_iso]: https://mirrors.tuna.tsinghua.edu.cn/ubuntu-releases/20.04/ubuntu-20.04-desktop-amd64.iso

本项目所使用的“远程”服务器为一台安装在本地虚拟机里的 `Ubuntu Server 20.04 x64`，镜像可从 [ubuntu-releases/20.04][ubuntu_mrs] 获取，具体的镜像文件是 [`ubuntu-20.04-live-server-amd64.iso`][ubuntu_iso]。

虚拟机软件建议使用 `VMware` 或 `VirtualBox`。

### 1.1.1 初始环境

- 网络
  - 宿主机设置虚拟机网络为 NAT 模式
- Linux 用户
  - 安装操作系统时按照流程设置用户：`octobug`
- OpenSSH
  - 安装操作系统时选择安装 OpenSSH Server

以上为安装 `Ubuntu Server 20.04 x64` 时自定义的少量配置，其余均为默认配置。

安装后虚拟机的登录界面如下：

![Initail Server](./images/01_01_init_ubuntu.png)

如图所示，安装后 DHCP 分配的 IPv4 为 `192.168.242.162`。

## 1.2 安装 Ansible

Ansible 只需要在操作机器上安装，安装之后就可以使用 Ansible 基于 SSH 来操作远程主机。远程主机除了安装 OpenSSH Server 之外，不需要安装任何其他和 Ansible 相关的软件。因此说 Ansible 是“无客户端的”，无需担心远程主机因为安装的自动化工具存在漏洞而被入侵。

[ins_guide]: https://docs.ansible.com/ansible/latest/installation_guide/index.html

官网有非常详细的安装文档：[Installation Guide][ins_guide]

```bash
$ ansible --version
ansible 2.8.4
  config file = None
  configured module search path = ['/Users/octobug/.ansible/plugins/modules', '/usr/share/ansible/plugins/modules']
  ansible python module location = /usr/local/Cellar/ansible/2.8.4_1/libexec/lib/python3.7/site-packages/ansible
  executable location = /usr/local/bin/ansible
  python version = 3.7.4 (default, Jul  9 2019, 18:13:23) [Clang 10.0.1 (clang-1001.0.46.4)]
```

## 1.3 测试

### 1.3.1 测试使用 Ansible 连接服务器

使用 **1.2** 小节安装的 Ansible 访问 **1.1** 小节中安装的“远程”服务器。

由于服务器上还没有 SSH 登录到 octobug 账户的公钥，所以先尝试用密码连接主机：

```bash
ansible all -i "192.168.242.162," -m ping -u octobug -k
```

- `-i`：用于指定 `inventory` 文件，inventory 里面记录的是主机的 IP 信息，但此处先不使用 inventory 文件，而是用一个字符串代替 `192.168.242.162,`（注意后面要有个逗号）
- `all`：作用于 inventory 文件中的所有主机，这个例子中只有一个主机 IP 地址
- `-m`：指定 Ansible 模块，这个例子指定了 `ping` 模块
- `-u`：指定登录远程主机的账户名
- `-k`：使用密码登录

更多参数请查看 `ansible -h`。

```bash
$ ansible all -i "192.168.242.162," -m ping -u octobug -k
SSH password:
192.168.242.162 | FAILED! => {
    "msg": "to use the 'ssh' connection type with passwords,
      you must install the sshpass program"
}
```

显然，此时需要本机安装有 sshpass 来提供 SSH 密码。然而使用 sshpass 容易造成安全问题，macOS 上最流行的包管理器 `brew` 甚至直接拒绝加入 sshpass 这个包：

```bash
$ brew install sshpass
Updating Homebrew...
Error: No available formula with the name "sshpass"
We won't add sshpass because it makes it too easy for novice SSH users to ruin
  SSH's security.
```

[sshpass]: https://groups.google.com/forum/#!topic/ansible-project/VXxqo88x1Zc

相关话题：[Using ansbile and ansbile-playbook with just the password for the remote host][sshpass]

> Ansible doesn't require openssh-server on the control machine, but when you are using ssh with password the program "sshpass" is needed on the control machine since ssh doesn’t provide this functionality itself.

既然如此，我们改用 SSH Key 登录。

### 1.3.2 `ssh-copy-id`

```bash
$ ssh-copy-id octobug@192.168.242.162
/usr/bin/ssh-copy-id: INFO: Source of key(s) to be installed: "/Users/octobug/.ssh/id_rsa.pub"
/usr/bin/ssh-copy-id: INFO: attempting to log in with the new key(s), to filter out any that are already installed
/usr/bin/ssh-copy-id: INFO: 1 key(s) remain to be installed -- if you are prompted now it is to install the new keys
octobug@192.168.242.162's password:

Number of key(s) added:        1

Now try logging into the machine, with:
  "ssh 'octobug@192.168.242.162'" and check to make sure that only the key(s) you
  wanted were added.
```

### 1.3.3 重新测试

这次将会使用 SSH Key 登录，因此不指定 `-k`。

```bash
$ ansible all -i "192.168.242.162," -m ping -u octobug
192.168.242.162 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3"
    },
    "changed": false,
    "ping": "pong"
}
```

- `"ansible_facts"`：Ansible 关心并收集的远程主机基本信息
- `"changed": false`：远程主机相关状态未被 Ansible ping 模块改变
- `"ping": "pong"`：ping 模块的执行结果

至此，本地操作机已经可以和“远程”主机正常通信。

下一节，将介绍 Ansible 中的一些常见术语，并使用 Ansible Playbook 来对这台服务器完成一些基本的系统配置。

### 1.3.4 Ansible 使用 `ping` 做了些什么？

### 1.3.5 保存快照

成功安装后关闭虚拟机并保存当前快照，这份快照将在后面派上用场。
