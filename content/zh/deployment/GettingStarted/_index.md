---
title: "服务器部署入门"
linkTitle: "服务器部署入门"
weight: 1
type: docs
---

[官方教程（英文）](https://foundryvtt.com/article/tutorial/)

[官方更新日志（英文）](https://foundryvtt.com/releases/)

---

通常而言，服务器部署按以下流程进行：
1. 挑选、购买云服务器
2. 如果使用域名：购买域名，并将域名绑定在服务器上
3. 登入服务器，开始部署 FVTT
4. 如果部署完成后无法打开：检查云服务器防火墙设置

## 服务器部署方式对比

| 对比项 | [Docker](docker) | [Windows](windows) | [Ubuntu](ubuntu)/[CentOS](centos) 原生 | 备注 |
| ---- | ---- | ---- | ---- | ---- |
| 价格 | ➖ | ➖ | ➖ | Win 可能会更贵 |
| 性能 | ➖ | 🤔 | ➖ | Win 会消耗更多内存，体验可能无感知 |
| 上手难度 | 🤔 | 😎 | 🤔 | Win 有图形界面，便于上手 |
| 文件管理 | ➖ | 😎 | ➖ | Win 有资源管理器，和 PC 一致 |
| 维护成本 | ➖ | 😎 | ➖ | Linux 原生有时需要注意运行环境问题 |
| 部署自动化程度 | 😎 | 🤔 | 😎 | 中文社区 Docker/CentOS 均有脚本 |