---
date: '2026-02-08T17:00:00+08:00'
summary: "找到mac上面dbeaver保存的账号密码信息"
title: '获取dbeaver保存的数据库密码信息'
categories: "学习"
---
## 前言

DBeaver 是一款非常好用的通用数据库管理工具，支持 MySQL、PostgreSQL、达梦、Oracle 等几乎所有主流数据库。但它有一个“不太友好”的设计：**默认情况下不允许直接查看已保存的连接密码**（出于安全考虑）。

时间长了，难免会忘记某个连接的密码。这时很多人会选择删除重连，但如果连接配置很复杂（驱动、参数、SSH隧道等），重新配置就很麻烦。

好消息是：DBeaver 把连接信息（包括加密后的密码）保存在本地配置文件中，我们可以通过简单几步找到并解密查看。本文整理了最常用、最可靠的方法（Windows为主，Linux/Mac类似）。

**重要安全提醒**  
- 本方法仅限**你自己电脑**上查看自己保存的密码。  
- 不要把 credentials-config.json 文件发给别人，也不要上传到公共仓库。  
- 生产环境建议不要长期保存密码在客户端，改用主密码保护或环境变量方式。

## 方法一：最简单粗暴的方式（推荐新手）

### 步骤1：找到工作空间路径

1. 打开 DBeaver  
2. 点击菜单：**窗口（Window） → 首选项（Preferences）**  
3. 在左侧导航找到：**常规（General） → 工作空间（Workspace）**  
4. 右侧会显示 **工作空间路径（Workspace location）**，类似下面路径（Windows为例）：
`C:\Users\你的用户名\AppData\Roaming\DBeaverData\workspace6`
`/Users/ishuliang/Library/DBeaverData/workspace6/General/.dbeaver`
复制这个路径。

### 步骤2：定位加密配置文件

1. 用文件资源管理器（或Total Commander等）打开上面复制的路径  
2. 进入子目录：**General** 或 **General.dbeaver**（视版本略有差异）  
3. 找到文件：**credentials-config.json**

这个文件就是存储所有连接加密密码的地方。

### 步骤3：解密查看密码（需要OpenSSL）

credentials-config.json 是加密的，不能直接打开看明文，需要用 OpenSSL 解密。

#### Windows 用户解密步骤

1. **安装 OpenSSL**（如果没有）  
- 推荐从官网下载 Win64 OpenSSL（https://slproweb.com/products/Win32OpenSSL.html）  
- 安装后记住安装目录（如 `C:\Program Files\OpenSSL-Win64\bin`）

2. 把 `credentials-config.json` **复制一份**到桌面或其他方便位置（防止误操作原文件）

3. 打开 **命令提示符（CMD）** 或 **PowerShell**，cd 到 OpenSSL 的 bin 目录：

```bash
cd "C:\Program Files\OpenSSL-Win64\bin"
4. 执行解密命令（替换路径为你的实际文件位置）：
```sh
openssl enc -d -aes-256-cbc -a -in C:\Users\你的用户名\Desktop\credentials-config.json -out decrypted.txt -pass pass:dvkvnfu84nr24nifasd0948y12n4ui1nasp9y8124y9182yn91284y12
```
> 注意：上面 -pass pass:xxx 中的密钥是 DBeaver 早期版本常用的固定密钥（社区已公开）。新版本可能变了，如果失败请搜索 “dbeaver credentials-config.json 密钥” 或查看 GitHub 项目如 geekyouth/crack-dbeaver-password。


或者直接执行(在控制台输出文本)：
`openssl aes-128-cbc -d -K babb4a9f774ab853c96c2d653dfe544a  -iv 00000000000000000000000000000000 -in credentials-config.json | dd bs=1 skip=16 2>/dev/null`

解密成功后，打开生成的 decrypted.txt，里面就是明文 JSON，搜索你的数据库连接名（如 "localhost_mysql"），就能找到对应的密码字段。
