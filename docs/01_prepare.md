# 1. 环境准备

## 1.1 一台全新的 Linux 服务器

[ubuntu_mrs]: https://mirrors.tuna.tsinghua.edu.cn/ubuntu-releases/20.04/
[ubuntu_iso]: https://mirrors.tuna.tsinghua.edu.cn/ubuntu-releases/20.04/ubuntu-20.04-desktop-amd64.iso

本项目所使用的“远程服务器”为一台安装在本地虚拟机里的 `Ubuntu Server 20.04 x64`，
镜像可从 [ubuntu-releases/20.04][ubuntu_mrs] 获取，具体的镜像文件是 [`ubuntu-20.04-live-server-amd64.iso`][ubuntu_iso]。

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

### 1.1.2 保存快照

成功安装后关闭虚拟机保存当前快照，这份快照将在后面派上用场。

## 1.2 安装 Ansible

Ansible 只需要安装在操作机器上，之后通过 SSH 操作远程主机。远程主机除了安装 OpenSSH Server 之外，不需要安装任何其他软件。因此说 Ansible 是“无客户端的”，无需担心远程主机因为自动化工具存在漏洞而被入侵。

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
