---
title: "事先检查 P2P 能否联机"
linkTitle: "事先检查"
weight: 1
type: docs
---

P2P 是否连接成功对连接双方的网络都有要求，简单来说，需要确保双方的 NAT 类型之间可以连接。

| NAT 类型 | 开放 | 中等 | 严格 |
| ---- | ---- | ---- | ---- |
| 开放 | ✔ | ✔ | ✔ |
| 中等 | ✔ | ✔ | ❌　|
| 严格 | ✔ | ❌ | ❌ |

可以看出，如果启动 FVTT 的环境的 NAT 类型是开放，则一般可以连接上（但并不保证网络连接质量好）。

## 如何测试 NAT 类型
在 Windows 下，我们使用 [NatTypeTester](https://github.com/HMBSbige/NatTypeTester) 来检测本地的 NAT 类型。

下载链接：[点此](https://github.com/HMBSbige/NatTypeTester/releases/download/2.2/NatTypeTester.7z)，解压后运行，点击 Test 即可进行测试
> 可以尝试测试第二次以触发 Windows 防火墙放行，放行后可能会检测出更开放的 NAT 类型

![nat-type-tester](/images/deployment/nat-typetest.png)

如果如图中所示， `NAT type`一栏显示 `FullCone` 即代表 NAT 类型为开放，P2P 连接性较好。

如果是其他情况，尤其是 `Symmetric` 或者 `UdpBlocked`，基本上难以直接稳定地建立 P2P 连接。

## 如何测试是否公网 IP
国内运营商一般不会主动分配给用户公网 IP，通过客服或许可以申请公网 IP，常用理由如玩游戏无法连接上等等（事实确实如此）。

- 查看自己路由器上分配到的 WAN 口 IP 地址
- 在 [IPIP](https://www.ipip.net/) 上查看自己暴露出的 IP 地址

如果这两个 IP 地址一致，则基本上应该是获得了公网 IP。

如果拥有公网 IP，其他玩家可以直接通过公网 IP 来访问启动的 FVTT。如果没有，最好使用[虚拟局域网或内网穿透](../normal/#内网穿透)等工具辅助建立连接，或者直接使用[服务器部署](../../gettingstarted)。
