# Ansible-by-Example

[![license][license]][license_link]

[license]: https://img.shields.io/github/license/itechub/Ansible-by-Example.svg
[license_link]: https://github.com/itechub/Ansible-by-Example/blob/master/LICENSE

## 介绍

本项目将通过若干运维实例演示如何使用 Ansible 管理一台全新的 Linux 服务器。

### 关于 Ansible

Ansible 是一款轻量级的 IT 运维自动化工具，现已被知名厂商 Red Hat 收购。

[ansible_site]: https://ansible.com/
[ansible_repo]: https://github.com/ansible/ansible

[Ansible GitHub Repo][ansible_repo]：

> Ansible is a radically simple IT automation system. It handles configuration management, application deployment, cloud provisioning, ad-hoc task execution, network automation, and multi-node orchestration. Ansible makes complex changes like zero-downtime rolling updates with load balancers easy. More information on [the Ansible website][ansible_site].

## FAQ

### 为什么选择 Ansible？

[it_auto]: https://www.ansible.com/overview/it-automation

> 摘自官网 [Why Ansible?][it_auto]

- 简单：快速提高生产力
  - 使用 YAML 编写 Ansible 脚本，简单易读
  - 不需要特定语言的编程基础
  - 任务按编排顺序清晰执行
- 强大：管理应用生命周期
  - 应用部署
  - 配置管理
  - 工作流编排
- 无客户端（指目标服务器）：可预测、可靠且安全
  - 无客户端架构
  - 基于 OpenSSH 和 WinRM（Windows 远程管理） 工作
  - 无需担心客户端安全更新问题

## 目录

### [1. 环境准备](docs/01_prepare.md)

#### [1.1 一台全新的 Linux 服务器](docs/01_prepare.md#11-一台全新的-linux-服务器)

#### [1.2 安装 Ansible](docs/01_prepare.md#12-安装-ansible)

#### [1.3 测试](docs/01_prepare.md#12-测试)

### 2. Linux 基础配置

## 参考

[best_prac]: https://docs.ansible.com/ansible/latest/user_guide/playbooks_best_practices.html
[ulsa_hb]: https://www.goodreads.com/book/show/8772005-unix-and-linux-system-administration-handbook

- Ansible Docs
  - [Best Practices][best_prac]
- [UNIX and Linux System Administration Handbook][ulsa_hb]
