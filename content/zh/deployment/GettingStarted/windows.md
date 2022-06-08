---
title: "Windows 部署 FoundryVTT 教程"
linkTitle: "Windows 部署教程"
weight: 26
type: docs
---

> 本篇教程参考 FVTT 英文社群网站 [Windows 托管教程](https://foundry-vtt-community.github.io/wiki/Windows/)和[猫斯基的 Windows Server 部署教程](http://www.goddessfantasy.net/bbs/index.php?topic=117014.0)。

---

## 选择你的安装方式

首先，你得租用一台云服务器。不管运营商是阿里云、腾讯云还是百度云，同系统的操作都是一样的。本教程使用的是腾讯云服务器。

FoundryVTT 在 Windows 服务器上有两种部署方式：1 是使用 EXE 版本直接安装；2 是使用 Node.js 版本进行安装。
二者相较起来，EXE 版本比较接近平时的 Windows 使用操作，适合刚入门的初学者；Node.js 版本需要一些指令操作，适合有一定基础的用户。
在实际的测试中，二者不存在实际的性能差距，仅仅是操作和运行方式不同，所以请不要担心。

### EXE 版本
---

1. 进入你的服务器运营商网页后台，设置你的远程连接密码
2. 在[Foundry VTT 官网](https://foundryvtt.com/)下载 FoundryVTT 的 `.exe` 安装包，右键`复制`（或<kbd>Ctrl+C</kbd>）

    - ![](/images/deployment/winserver/03-download.png)

3. 在你的桌面上右键 `开始` → `运行`（或者按 <kbd>Win+R</kbd>），输入 `mstsc`，确定

    - ![](/images/deployment/winserver/00-connect.png)

4. 输入 `你的服务器IP` 连接

    - ![](/images/deployment/winserver/01-mstsc.png)

5. 输入 `你的登录密码` 确定，进入服务器远程桌面

    - ![](/images/deployment/winserver/02-password.png)

6. 打开 `C` 盘，右键粘贴（或<kbd>Ctrl+V</kbd>）你刚才复制的 `FoundryVTT.exe` 安装包，双击安装
7. 安装完成后，双击桌面图标运行 FVTT，输入授权 KEY，然后点击 `agree` 同意安装协议
8. 右键 `开始` → `运行`（或者按 <kbd>Win+R</kbd>）,输入 `firewall.cpl`，打开 `Windows Defender 防火墙`，点击左方 `允许应用或功能通过 Windows Defender 防火墙`

    - ![](/images/deployment/winserver/firewalls2.png)

在列表中找到 `FoundryVTT`，勾选右边的两个选项，然后保存。
（如果列表中没有，点击 `允许其他应用` 并添加 FoundryVTT）

9. 开放访问端口。进入你的服务器控制台，找到安全组，或者出入站规则（每个服务器运营商稍微不一样，这里以腾讯云为例子）。
在左侧边栏中点击`安全组`，默认会给你分配一个default的组，点击右边`修改规则`
点击`添加规则`，来源填入`all`,协议填入`TCP:30000`，点击完成。

到这里，你就部署成功啦~（撒花）
现在你可以关闭远程桌面，在你的电脑上使用浏览器访问 `http://[服务器IP]:30000`，就能直接使用FVTT了！

注意事项：
- 即使关闭远程桌面，只要 FoundryVTT 程序还在运行着就可以使用。
- 某些选项会让你重启 FoundryVTT 程序，若程序关闭了，此时你需要登录到远程桌面重新手动启动 FoundryVTT。
- 存储在本地的资源可直接使用 <kbd>Ctrl+C</kbd>, <kbd>Ctrl+V</kbd> 命令复制到远程桌面文件夹中。

### 创建远程桌面登陆快捷方式

如果你不想每次都点击 `运行` 来连接你的远程桌面，那么可以为其创建一个快捷登陆方式（`.RDP` 文件）。
（某些服务器运营商后台就提供 RDP 文件下载，如果没有，你可以按照下面的方法创建一个。）

首先，还是 `运行` → `mstsc`，点击确定打开下一级对话框。

- ![](/images/deployment/winserver/01-mstsc.png)

点击左下角的 `显示选项`，在下方 `连接设置` 栏目中，选择 `另存为..`，保存RDP到你的桌面或任意文件夹中。
下次你想要登陆你的远程桌面时，双击打开这个RDP文件即可立即连接。

### Node.js 版本
---

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
> 服务器 IP 一般是云服务器控制台网页上的公网 IP

### 在腾讯云 Windows Server 服务器上部署的教程视频

> 从 26:12 秒开始是部署过程

{{< bilibili BV1gE411A7c4 >}}
