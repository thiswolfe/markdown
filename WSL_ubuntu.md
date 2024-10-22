---
title: windows安装WSL子系统ubuntu
description: 适用于 Linux 的 Windows 子系统 (WSL) 是 Windows 的一项功能，可用于在 Windows 计算机上运行 Linux 环境，而无需单独的虚拟机或双引导
tag:
 - wsl
 - ubuntu
categories:
 - linux-tools
---

WSL 旨在为希望同时使用 Windows 和 Linux 的开发人员提供无缝高效的体验。

## 启用子系统对应功能

1. 打开运行  `win + r`
2. 然后输入 `optionalfeatures` 按 `enter` 按键
3. 打开 `windows功能` - `启用或关闭windows功能` 窗口
4. 勾选 `适用于Linux的Windows子系统`  和 虚拟化相关的选项
5. 确定，之后系统重启，等待系统更新完成


## 下载安装 ubuntu 子系统

1. 通过下面的链接安装你喜欢 ubuntu 的 wsl 版本

    link: [ubuntu - Microsoft Apps](https://apps.microsoft.com/search?query=ubuntu&hl=en-us&gl=US)

    推荐版本链接：[Ubuntu 22.04.2 LTS](https://www.microsoft.com/store/productId/9PN20MSR04DW?ocid=pdpshare)

2. 打开对应图标
3. 根据提示输入用户名(username)和密码(password)


## 安装/更新 wsl 2

1. 点击下面链接，下载 x64 版本的 wsl 更新

    x64: [wsl_update_x64.msi](https://wslstorestorage.blob.core.windows.net/wslblob/wsl_update_x64.msi)

2. 下载完成之后，安装msi程序


## wsl默认版本设置为WSL 2

请在cmd中运行，下面的命令

      wsl --set-default-version 2


## 检查WSL 版本

打开命令行 `cmd` 检查每个发行版的 WSL 版本：

      wsl -l -v


## ！错误处理


### 8007019e

> WslRegisterDistribution failed with error: 0x8007019e

出现这个错误的原因是没有安装Windows子系统支持，如果前面打开那个功能过后就重启就不会出现这个问题

##### 解决办法：

1. 打开(win+x) Windows PowerShell(Admin)  

2. 输入下面内容之后，回车(Enter)执行，等待系统重启

      Enable-WindowsOptionalFeature -Online -FeatureName Microsoft-Windows-Subsystem-Linux 


### 800701bc

> WslRegisterDistribution failed with error: 0x800701bc

造成该问题的原因是WSL版本由原来的WSL1升级到WSL2后，内核没有升级

下载安装适用于 x64 计算机的最新 WSL2 Linux 内核更新包即可

##### 解决办法：

1. 下载并安装WSL2更新包
2. 将WSL2设置成默认


### 80370102

> WslRegisterDistribution failed with error: 0x80370102

这个只要前面开启那个虚拟化平台也不会报错


### 80004005

> WslRegisterDistribution failed with error: 0x80004005

LxssManager 服务启动异常

##### 解决方法：

将 LxssManager 服务修改为自动启动
从服务中修改会提示“拒绝访问”，所以从注册表改

1. 按 `win + r` 启动运行
2. 输入 `regedit` 打开注册表
3. 找到这个位置 `\HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\LxssManager`
4. 右键 Start 项 -> 将值修改为 2（默认为 3）

      > 1-自动(延迟启动)； 2-自动； 3-手动； 4-禁用

5. 更新wsl
   5.1 管理员启动 `powershell`
   5.2 输入 `wsl --update`  成功安装


### 80070005

> WslRegisterDistribution failed with error: 0x80070005

##### 未知错误 ？？？

恢复之前的所有操作，重启系统，之后再安装一遍
要是还不行，用重装大法吧，法术失效了.... (^_^) !
