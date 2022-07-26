---
title: 2022-07-26-windows系统shift加右键管理员打开CMD
date: 2022-07-26 20:46:49
tags: CMD
---

```
Windows Registry Editor Version 5.00
 
[HKEY_CLASSES_ROOT\Directory\shell\cmd]
@="在此处打开命令提示符"
"Icon"="cmd.exe"
 
[HKEY_CLASSES_ROOT\Directory\Background\shell\cmd]
@="在此处打开命令窗口"
"Icon"="cmd.exe"

 
[HKEY_CLASSES_ROOT\Drive\shell\cmd]
@="在此处打开命令窗口"
"Icon"="cmd.exe"

 
[HKEY_CLASSES_ROOT\LibraryFolder\background\shell\cmd]
@="在此处打开命令窗口"
"Icon"="cmd.exe"
 
 
 [HKEY_CLASSES_ROOT\Directory\Background\shell\runas]
@="在此处打开命令提示符（管理员）"
"Icon"="cmd.exe"
"Extended"=""

[HKEY_CLASSES_ROOT\Directory\Background\shell\runas\command]
"ShowBasedOnVelocityId"=dword:00639bc8
@="cmd.exe /s /k pushd \"%V\""

```
