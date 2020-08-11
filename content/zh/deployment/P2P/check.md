---
title: "事先检查 P2P 能否联机成功"
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

如果是其他情况，尤其是 `Symmetric` 或者 `UdpBlocked`，基本难以直接稳定地建立 P2P 连接，最好使用内网穿透等工具辅助建立连接，或者直接使用服务器部署。