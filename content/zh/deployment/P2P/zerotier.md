---
title: "使用 ZeroTier 进行 P2P 联机"
linkTitle: "ZeroTier 联机"
weight: 11
type: docs
---

ZeroTier 是一款开源的构建虚拟网域的工具，同时其公司直接提供包含免费层的服务，可以用来辅助 P2P 联机。比较遗憾的是，它的服务器位于国外，导致国内玩家连接质量并不是特别理想，*但相对于无法连接总是更好的*。

[ZeroTier One 官网下载链接](https://www.zerotier.com/download/)

## GM 使用方法
1. 下载并安装 ZeroTier One
2. 在[官网登录页](https://my.zerotier.com/login)注册 ZeroTier 账号，免费订阅可以满足需求
3. 注册完毕后，打开[网络管理](https://my.zerotier.com/network)，创建一个新的网络，并设置为公开
    - 创建新网络：![](/images/deployment/zerotier/net.png)
    - 设置为公开：![](/images/deployment/zerotier/set_public.png)
4. 复制网络 ID（就是类似 `af415e486f9a53de` 这一串）
5. ZeroTier 会在系统托盘（右下角）显示图标
    - 点击图标打开菜单，选择加入网络(join a network)，然后粘贴刚才复制的网络 ID 并确认
    - 将网络 ID 发送给玩家，并让玩家们遵照 [PL 使用方法](#pl-使用方法)来加入虚拟局域网
6. 打开 ZeroTier 窗口
    - 窗口中会显示网络信息：<br>![](/images/deployment/zerotier/local.png)
    - 复制自己的托管 IP(Managed IPs)，其他玩家[**加入虚拟网络后**](#pl-使用方法)通过 `http://[托管IP]:30000` 访问开启的 FVTT（不需要斜杠和后面的数字）
    - 比如按图中信息，其他玩家访问 `http://10.144.100.52:30000` 即可
    - 如果 FVTT 设置的端口号不是 30000，那么需要对应调整其他玩家访问 FVTT 的地址

## PL 使用方法
1. 下载并安装 ZeroTier One
2. ZeroTier 会在系统托盘（右下角）显示图标
    - 点击图标打开菜单，选择加入网络(join a network)，粘贴 GM 发送的的网络 ID 并确认

---

{{% alert title="注意" color="warning" %}}
ZeroTier 安装后会作为系统服务，跟随系统启动，如果不关闭或者设置为手动，将会始终连接到创建的虚拟网络中
{{% /alert %}}