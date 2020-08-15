---
title: "一般情况下的 P2P 使用教程"
linkTitle: "启动教程"
weight: 6
type: docs
---

> 如果你的网络质量不错，并且拥有公网 IP，可以参见此文在本地运行 FVTT 并联机。
> 本篇教程参考 FVTT 英文社群网站 [Windows 托管教程](https://foundry-vtt-community.github.io/wiki/Windows/)以及[官方安装教程](https://foundryvtt.com/article/installation/)。

---

## 基础配置
1. 登录 [Foundry VTT 官网](https://foundryvtt.com/)，下载最新的 Windows 版本安装包（图中第一个）
    - ![](/images/deployment/download-url.png)
2. 打开，开始安装 FVTT，一路按照指示进行安装
    - 如果选择安装后打开 FVTT，可能会出现防火墙提示，点击`允许访问`
3. 如果看到如下的窗口，证明你已经成功安装了 FVTT
    - ![](/images/deployment/local-run.jpg)

之后如果需要再运行 FVTT，直接打开快捷方式或者运行安装目录下的 `FooundryVTT.exe`。

如果你拥有公网 IP，接下来让其他玩家通过 `http://[你的公网 IP]:30000` 访问（比如 `http://1.2.3.4:30000`）。

或者可以查看 Foundry VTT 所显示的游戏邀请链接：

在 FVTT 软件中已经启动的世界里，打开侧边栏上齿轮状的**设置**(Settings)，点击**邀请链接**(Invitation Links)，查看可以访问的链接。

- **局域网**(Local Network)，只能在局域网环境下访问，比如同一个路由器下的其他设备
- **互联网**(Internet)，暴露到互联网上的地址，但如果公网 IP 不可访问，这个地址无效

{{% alert title="Tips" color="warning" %}}
如果需要自己连到自己开启的 FVTT 游戏中（比如用浏览器测试玩家视角），应该使用 localhost 而不是 IP 地址，比如 `http://localhost:30000`
{{% /alert %}}

---

### 内网穿透

如果你和其他玩家无法通过互联网地址访问你的 FVTT，那么接下来需要构建虚拟局域网，或者使用内网穿透把端口转发到公网来让玩家可以访问，典型的解决方案如下：

- [ZeroTier](../zerotier)：虚拟局域网
- [蒲公英](https://pgy.oray.com/)：虚拟局域网（暂未提供教程）
- [ngrok](https://ngrok.com/)：内网穿透（暂未提供教程）
- [NATAPP](../natapp)：内网穿透
- [花生壳](https://hsk.oray.com/)：内网穿透（暂未提供教程）
