---
title: "使用 NATAPP 进行 P2P 联机"
linkTitle: "NATAPP 联机"
weight: 21
type: docs
---

NATAPP 相比 ngrok 的优势是使用国内服务器进行转发，对于位于国内的玩家来说连接质量会好很多。NATAPP 提供免费层可供轻度使用，如果需要跨境连接，同时也提供多线和香港接入的收费服务。

---

## 设置 NATAPP

购买 NATAPP 以及基础使用参见其[官网教程](https://natapp.cn/article/natapp_newbie)。

不过，在购买隧道时，本地端口需要设置为 `30000`。
> 如果 FVTT 设置的端口号不是 30000，那么需要对应调整本地端口。

之后按照官网教程继续进行，完成后复制穿透网址，如 `http://ywy9n8.natappfree.cc`，让玩家们访问该地址即可。

{{% alert title="注意" color="warning" %}}
每次开启 NATAPP 开始转发后，因为免费隧道只能提供随机域名，所以访问的地址会出现变动，需要让玩家们每次使用新的链接
{{% /alert %}}
