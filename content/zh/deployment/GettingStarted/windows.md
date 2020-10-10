---
title: "Windows 部署 FoundryVTT 教程"
linkTitle: "Windows 部署教程"
weight: 26
type: docs
---

> 本篇教程参考 FVTT 英文社群网站 [Windows 托管教程](https://foundry-vtt-community.github.io/wiki/Windows/)和[猫斯基的 Windows Server 部署教程](http://www.goddessfantasy.net/bbs/index.php?topic=117014.0)。

---

如果你没有公网 IP，又不缺一台服务器的钱，那么你可以考虑租一台服务器并架设一个 FoundryVTT 在上面。

服务器有数种系统，例如 Windows、Linux、MacOS、Ubuntu 等。本教程以 Windows 系统为例。

那么我们假设你已经购买了服务器，并且安装好了 Windows Server 系统，连接上了**远程桌面**，接下来就进行部署：

## 基础配置
1. 下载并安装 Node.js，在 [Node.js 官网安装页面](https://nodejs.org/zh-cn/download/)上，点击 Windows 安装包下载
2. 将 Node.js 安装包通过 *远程桌面* 复制到服务器上，然后在服务器上点击安装包文件，一路确认，安装 Node.js
3. 从 [Foundry VTT 官网](https://foundryvtt.com/)上下载最新包，使用 Node.js 部署，所以选择第四项
    - ![](/images/deployment/download-url.png)
4. 将压缩包通过 *远程桌面* 复制到服务器上，直接拖拽或者 <kbd>Ctrl</kbd>+<kbd>C/V</kbd> 均可，然后解压到某文件夹内（比如桌面）
5. 在这个目录下，启动 CMD。如图所示，在资源管理器地址栏下输入 `cmd`，然后回车打开 CMD 窗口
    - ![](/images/deployment/win-unzip-cmd.png)
6. 接着运行这个命令
    ```powershell
    node .\resources\app\main.js
    ```

如果能够看到进行了类似以下的输出，那么证明启动成功。
```bash
FoundryVTT | 2020-08-15 14:50:37 | [info] Foundry Virtual Tabletop - Version 0.6.5
FoundryVTT | 2020-08-15 14:50:37 | [info] Running on Node.js - Version 14.8.0
```

在自己电脑的浏览器上输入 `http://[服务器IP]:30000` 即可访问 FVTT，然后就可以把远程桌面关闭了。

### 在腾讯云 Windows Server 服务器上部署的教程视频

> 从 26:12 秒开始是部署过程

{{< bilibili BV1gE411A7c4 >}}
