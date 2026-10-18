---
title: "关闭开启之后linuxx子系统异常"
date: 2022-11-18 14:53:40
---
在管理员权限下打开powershell，执行以下命令后重启电脑

```plaintext
# 启用适用于 Linux 的 Windows 子系统
dism.exe /online /enable-feature /featurename:Microsoft-Windows-Subsystem-Linux /all /norestart
# 启用虚拟机功能
dism.exe /online /enable-feature /featurename:VirtualMachinePlatform /all /norestart
```
