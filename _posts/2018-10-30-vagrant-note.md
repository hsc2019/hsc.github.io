---
title: 使用 Vagrant 打包本地的开发环境
date: 2018-10-30 17:56:39
categories:
- 运维
tags:
- vagrant
- homestead
---

# Vagrant 概述
Vagrant是一个基于Ruby的工具，用于创建和部署虚拟化开发环境。它 使用Oracle的开源VirtualBox虚拟化系统，使用 Chef创建自动化虚拟环境。

# 安装 vagrant 开发工具

这套工具需要安装 [vagrant](https://www.vagrantup.com/) 和 [virtualbox](https://www.virtualbox.org/wiki/Downloads) 两个软件，前者依赖于后者。
推荐去官网下载最新版本，可以避免一些坑。安装的时候一路默认就可以，如果是 Windows 系统，建议本地安装 git，通过鼠标右键唤起 git bash 来调用 vagrant 命令。

# Vagrant 的基本命令

## 管理 box 的命令

| 命令                                               | 解释               |
| -------------------------------------------------- | ------------------ |
| vagrant box list                                   | 查看目前虚拟机     |
| vagrant box add                                    | 新增一个虚拟机     |
| vagrant box remove                                 | 删除指定box        |
| vagrant package --output xxx.box                   | 打包               |
| vagrant package --output xxx.box --base 虚拟机名称 | 指定虚拟机名并打包 |

## 启动环境的命令

| 命令            | 解释              |
| --------------- | ----------------- |
| vagrant init    | 初始化配置vagrant |
| vagrant up      | 启动虚拟机        |
| vagrant ssh     | ssh登录虚拟机     |
| vagrant suspend | 挂起虚拟机        |

## 管理虚拟机的命令

| 命令            | 解释           |
| --------------- | -------------- |
| vagrant reload  | 重启虚拟机     |
| vagrant halt    | 关闭虚拟机     |
| vagrant status  | 查看虚拟机状态 |
| vagrant destroy | 删除虚拟机     |

## 高级功能

| 命令     | 解释                               |
| -------- | ---------------------------------- |
| 端口转发 | 将本机具体端口转发到指定虚拟机端口 |
| 共享目录 | 将本机具体目录和虚拟机共享         |
| 网络配置 | 三种网络配置方式                   |

## 配置

本文参考自慕课网的视频，更多的资料参考 [Vagrant 使用教程](https://github.com/apanly/mooc/tree/master/vagrant)。以下列出一个简单的配置文件。

```
# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
  config.vm.box = "ubuntu1404_imooc"
  config.vm.hostname = "mooc"
  config.vm.network "forwarded_port", guest: 80, host: 8888 ,id: 'nginx'
  config.vm.network "forwarded_port", guest: 8888, host: 8889 ,id: 'apache'
  config.vm.network "private_network", ip: "192.168.33.111",auto_config: true
  config.vm.synced_folder "E:/Vbox/Users/vincent/code", "/home/www", :nfs => true
  #config.vm.synced_folder "/Users/vincent/code/", "/home/www", :nfs => true

  config.vm.provider "virtualbox" do |vb|
  #   # Display the VirtualBox GUI when booting the machine
  #   vb.gui = true
  #
  #   # Customize the amount of memory on the VM:
      vb.memory = "1024"
      vb.cpus = 2
      vb.name = "ubuntu1404_imooc"
  end


  # Enable provisioning with a shell script. Additional provisioners such as
  # Puppet, Chef, Ansible, Salt, and Docker are also available. Please see the
  # documentation for more information about their specific syntax and use.
  # config.vm.provision "shell", inline: <<-SHELL
  #   apt-get update
  #   apt-get install -y apache2
  # SHELL
end
```

# 定制的vagrant：homestead

homestead 是为 laravel 定制的 vagrant 开发环境，其中内置了较为完备的开发环境。并且是 laravel 官方推荐的开发工具。熟练掌握 homestead 可以显著提高开发效率。
homestead 的使用方法可以参考 Laravel 的[官方文档](https://laravel.com/docs/6.x/homestead)。这是 GitHub 上 [homestead 的源码](https://github.com/laravel/homestead)