---
title: "系统/MOD"
linkTitle: "♟ 系统/MOD"
weight: 31
type: docs
menu:
  main:
    weight: 31
---

社区内容是 Foundry VTT 的灵魂，缺少社区贡献的游戏系统和五花八门的功能的 MOD，或许 FVTT 并不会发展到像今天这般有口皆碑。显然，也正是在 Atropos 的精心的架构和开放的态度下，才诞生了如此拥抱社区的跑团软件和互帮互助的社群。

---

基于 FVTT 足够开放的 API、宏，使用 JavaScript，社区已经构建出了不少耳熟能详的游戏系统，作为 FVTT 上进行游戏的基石；也制作出了一个又一个解决跑团痛点，或者奇思妙想的 MOD。

> 注意， FVTT 本身并不含核心的游戏规则，你需要安装游戏系统来为你的世界设定一个基础游戏规则，比如 `dnd5e`。

[游戏系统/MOD 官方合集（内容可能来自第三方）](https://foundryvtt.com/packages/)

## 如何使用国内镜像解决系统和 MOD 难以安装的问题？

众所周知，由于墙和出口带宽的原因，国内服务器或者国内无梯用户安装游戏系统和 MOD 时候的下载速度比较缓慢或者没有速度，表现出来的结果就是常常系统/MOD 安装失败。

为此 FVTT-CN 社区开发了 [Astral Projection（星界投影）](https://github.com/fvtt-cn/AstralProjection)，用于将 *部分* 游戏系统和 MOD 同步镜像到国内的服务器和 CDN 上，并且提供下载服务，方便国内用户安装和更方便地使用 FVTT。

**使用方法是在安装前，替换清单文件（Manifest URL）的链接：**

比如当你需要安装 `dnd5e` 游戏系统时，从官网或中文社区 MOD 介绍中复制到的，它的清单文件地址是：`https://gitlab.com/foundrynet/dnd5e/raw/master/system.json`

**在这个地址的 `https://` 的后面，加上一段 `cdn.sbea.in/`，即替换为国内镜像地址。**

上述例子中，用此法修改，链接就会变为：`https://cdn.sbea.in/gitlab.com/foundrynet/dnd5e/raw/master/system.json`

然后在 FVTT 中填写清单文件地址处，粘贴这个链接即可使用快速的国内镜像进行安装~

### 请求同步新的 系统/MOD
在 [GitHub 项目](https://github.com/fvtt-cn/AstralProjection/issues/new/choose)上提出新 issue，填写好 MOD 名称、项目地址、清单文件地址，即提出同步请求。

### 支持国内镜像运营
运营、维护国内镜像需要一定成本，如果愿意的话，可以在[爱发电](https://afdian.net/@mitch)上支持 FVTT-CN 的开发和维护。
