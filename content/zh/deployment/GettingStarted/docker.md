---
title: "Docker 部署 FoundryVTT 教程"
linkTitle: "Docker 部署教程"
weight: 21
type: docs
---

Foundry VTT 目前拥有若干个来自不同用户实现的 Dockerfile，这些实现中，较为推荐 [felddy/foundryvtt](https://github.com/felddy/foundryvtt-docker) 的镜像，而使用该教程中提供的 Linux 自动部署脚本，可以简化操作，自动部署，无需学习 Docker 的使用和管理。

## 介绍

目前可以通过中文社区的自动脚本进行容器化部署，也可以手动启动容器。

在全新购买的云服务器上使用自动脚本进行部署，最少只需要输入 Foundry VTT 站点上的用户名和密码即可完成，同时脚本也包含了后续更新、维护的方法。

### 使用 Docker 的优点
- 无需关心系统环境：只要是主流发行版的 Linux，甚至于 Windows 服务器，只要安装了 Docker，即和其他用户的部署行为一致，难以因系统环境问题出错
- 直接使用 Docker 的后台管理：Docker 自己会监控容器内运行的进程状态、网站可访问状态，如果意外情况导致 FVTT 崩溃或无法访问，可以不需要使用 pm2 等进程监控工具即可自动重启、还原
- 目前主流的 FVTT 的 Docker 镜像包含内置的自动获取下载地址和授权的脚本，可以简化部署流程
- ...

### 使用 Docker 的缺点
- 如果使用卷挂载，直接访问 FVTT 的数据文件会变得略困难（可以通过 Docker 启动其他容器简化文件访问，以下自动部署脚本已使用 Web 文件管理器解决该问题）
- 目前主流的 FVTT 的 Docker 镜像升级时需要重建容器，而不能通过 FVTT 内置更新（以下自动部署脚本可以大幅简化更新流程）
- ...

## 使用教程

### 自动部署脚本安装流程
1. 首先，假设已经登入了 Linux 服务器，并且能够获得 root 权限
    - 如果还没有 Linux 服务器，可以参见[云服务器指南](../cloud/#登入-linux-云服务器)，了解如何购买/基本操作
    - 你无需了解什么是 root 权限，云服务通常会提供；如果确认无法获取，可咨询客服
2. 下载脚本
    ```bash
    wget -O fvtt.sh https://fvtt-cn.coding.net/p/FoundryDeploy/d/FoundryDeploy/git/raw/master/fvtt.sh && sudo chmod +x fvtt.sh
    ```
3. 运行脚本，进行安装
    ```bash
    sudo ./fvtt.sh
    ```
    安装时会检查是否能获取 root 权限，并且如果没有安装 Docker 会自动安装。
    
    境内服务器下载镜像时可能会等待较长时间，可以耐心等待。

    然后脚本会提示输入参数，每条参数输入完成后回车即可，参数包含（✅：必需 | ⭕：可选）：
    ![](/images/deployment/docker-script-input.png)
    - ✅ FVTT 账号
    - ✅ FVTT 密码 （用于获取 FVTT 下载地址/授权信息）
    - ⭕ 版本号/下载地址
        - 直接回车跳过输入版本号，使用最新稳定版即可
        - **注意**：如果使用境内服务器，从 FVTT 官方下载安装包会比较慢，可以考虑粘贴[星界投影](../../../modules/#fvtt-软件镜像)的镜像下载源的 Linux 版本下载链接。**境内服务器推荐使用镜像；境外服务器建议直接输入版本号**！
        - Linux 版 FVTT （即本脚本需要使用的）的星界投影的镜像下载地址为：`https://cdn.sbea.in/foundry/linux/foundryvtt-0.7.9.zip`。如果需要使用其他版本，替换链接中的 `0.7.9` 即可
        - ✅ 如果跳过了 FVTT 账号/密码的输入，则必须输入直链下载地址而非版本号来安装，否则无法获取 FVTT 下载地址
        - 例：`0.7.9` 或 `https://cdn.sbea.in/foundry/linux/foundryvtt-0.7.9.zip`
    - ⭕ 自定义管理密码
        - 安装完成后，如果设置了这个密码，需要使用密码才能进入 FVTT 管理界面（游玩无需），强烈推荐进行设置；回车跳过，将不设置密码，使任意用户均可管理 FVTT （包括删除世界等权限）
        - 例：`password`
    - ⭕ FVTT 绑定域名
        - 如果为这台服务器绑定了给 FVTT 使用的域名（必须先绑定再安装），可以输入域名（不带 HTTP/HTTPS），否则直接回车跳过
        - 绑定域名的流程可参见[绑定（子）域名](../domain-name/#绑定子域名)
        - 例：`my.fvtt.com`（你已绑定该服务器的域名）
    - ⭕ FVTT CDN 加速域名
        - 如果想要使用 CDN，可以配置一个 CDN 加速域名，详情参见 [CDN 配置](../cdn)章节，否则直接回车跳过
        - 例：`cdn.fvtt.com`（你将设置的 CDN 回源使用的域名）
    - ⭕ 使用 Web 文件管理器
        - 为了管理文件方便，建议使用。直接回车跳过设置，默认会使用；如果不使用，输入`n`再回车
        - 例：直接回车（使用 Web 文件管理器）
    - ⭕ 如果使用 Web 文件管理器，可以绑定域名
        - 如果需要给 Web 文件管理器绑定域名使用（必须先绑定再安装），可以输入，否则直接回车跳过
        - 例：`file.fvtt.com`（你已绑定该服务器的域名）
    - ⭕ 使用 Docker 仪表盘
        - Docker 仪表盘（Portainer）用来方便的管理服务器上运行的容器（重启、监测状态等），比如 FVTT 和 Web 文件管理器等；默认不会使用，可以直接回车跳过
        - 例：直接回车（不使用仪表盘）
    - ⭕ 如果使用 Docker 仪表盘，可以绑定域名
        - 如果需要给 Docker 仪表盘绑定域名使用（必须先绑定再安装），可以输入，否则直接回车跳过
        - 例：`dashboard.fvtt.com`（你已绑定该服务器的域名）

    确认输入好的参数之后，便可以回车等待部署完成。

    *部署完成后，Foundry VTT 可能还需要等待一会儿，完成软件的下载*

4. 部署完成后，脚本会提示如何访问 FVTT，如图所示：
![](/images/deployment/docker-script-result.png)

**以上即是使用 Docker 脚本首次部署的全部流程，如果有无法连接等其他问题可以参见下方 [Docker 部署 FAQ](#docker-部署-faq)**。

{{% alert title="Tips" color="info" %}}
自动部署脚本的其他使用方法和详情，请参见[脚本仓库](https://github.com/fvtt-cn/FoundryDeploy)中的文档
{{% /alert %}}

### FVTT 升级/更新流程
Docker 容器为了保证运行时文件尽量不受改动，避免在不同时间启动容器产生了行为上的差异，使用了 `--noUpdate` 参数启动容器内的 FVTT，关闭了 Foundry VTT 自动更新的功能，使得用户无法使用 FVTT 内置的检查更新来升级。

为了解决这个问题，自动部署脚本包含了如何升级容器内 FVTT 版本的方法，这个方法会保证数据文件夹内的文件不受改动，可以放心升级，*但 FVTT 升级后再启动世界往往会对世界进行更新*。

**该方法不仅可以升级版本，还可以方便地用脚本重装同版本或者回退旧版本**。

#### 更新脚本
更新脚本和下载脚本命令相同，粘贴执行以下命令：
```bash
wget -O fvtt.sh https://fvtt-cn.coding.net/p/FoundryDeploy/d/FoundryDeploy/git/raw/master/fvtt.sh && sudo chmod +x fvtt.sh
```

#### 升级流程
1. 首先移除旧有容器
    ```bash
    sudo ./fvtt.sh remove
    ```
    按提示，输入 `y` 后回车确认，等待几秒后，旧版本容器即被成功删除

2. 重建容器，使用新版本
    ```bash
    sudo ./fvtt.sh recreate
    ```
    进入和安装流程非常类似的重建容器流程，默认使用已存储的配置文件，回车选择使用。

    - **版本号或者下载地址**：输入你想升级/回退的版本号或者对应下载地址即可
      - 境外服务器输入版本号比如 `0.7.9` 即可，境内服务器推荐使用星界投影镜像下载
      - 星界投影用法例如：`https://cdn.sbea.in/foundry/linux/foundryvtt-0.7.9.zip`，需要其他版本，替换其中`0.7.9`即可
    - 如果询问**是否需要清除 Caddyfile 内容**，如无特殊需求，回车跳过确认清除
      - 出现该提示的原因一般是之前使用了旧版脚本进行部署，而使用了更新脚本（`v1.4.0+`）进行 FVTT 更新，需要完全清空 Caddyfile
      - 清除 Caddyfile 不会影响之前安装的 FVTT 的世界、合集、模组等文件
    
{{% alert title="注意" color="warning" %}}
为了方便升级和二次部署，脚本运行后默认会存储输入的参数信息。如果不需要存储配置，避免如果服务器被黑之后账号信息可能的泄露，请在脚本运行完毕后删除 `fvtt-config` 文件，执行：
```
sudo rm fvtt-config
```
{{% /alert %}}

### 部署教程视频

{{< bilibili BV1Bv41167n6 >}}

## Docker 部署 FAQ

### 无法完成部署
部署过程中，由于指令输入错误、网络波动之类的原因，可能会出现下载错误，弹出 `错误：...` 一类消息并中断部署过程。根据弹出的错误提示，可以进行以下排错。

---

> 错误：安装 Docker 失败，请查看使用教程 FAQ 或联系脚本作者
<br/>
> 错误：Docker 无法启动容器，请查看使用教程 FAQ 或联系脚本作者

正在使用的 Linux 发行版可能内核版本过时或并非官方认可，以致无法通过官方脚本安装或者使用 Docker。

比如 Android 手机内核过时无法启动 Docker、群晖 NAS 无法通过官方安装脚本安装 Docker。

解决方法是使用 Linux 主流发行版操作系统，如： `Ubuntu 16.04+`, `CentOS 6+`, `Debian Stretch+`, `RHEL 7+`, `Fedora 32+` 等等。如果采用云服务器进行部署，通常在购买时以及购买后都可以任意选择需要安装的 Linux 发行版，尽量选择主流版本比如 `Ubuntu 20.04`。

---

> 错误：... 端口被占用，无法 ... 部署
<br/>
> 错误：... 已经启动过，请升级而非安装
<br/>
> 错误：... 容器启动失败

出现这种情况的通常原因是已经部署过 FVTT 并且没有按照升级流程的步骤，首先移除旧有容器，需要先执行：`sudo ./fvtt.sh remove` 以移除，具体步骤可以参见上文[升级流程](#升级流程)。

---

> 错误：拉取 ... 失败

境内服务器更容易出现该错误，当网络出现波动，多次尝试后仍然无法成功下载 Docker 镜像后，即报此错。

报错退出后，可以在再次执行脚本部署命令前，通过手动拉取解决该问题：
```bash
sudo docker pull ...
```
将 `...` 替换为拉取失败的镜像，镜像名称参见下表，境内服务器使用 `USTC 源镜像名` 一列：

| 报错名 | USTC 源镜像名 | 官方源镜像名 |
| ----  | ----  | ---- |
| FoundryVTT | docker.mirrors.ustc.edu.cn/felddy/foundryvtt:release | felddy/foundryvtt:release |
| Caddy | docker.mirrors.ustc.edu.cn/library/caddy | library/caddy |
| FileBrowser | docker.mirrors.ustc.edu.cn/filebrowser/filebrowser:alpine | filebrowser/filebrowser:alpine |
| Portainer | docker.mirrors.ustc.edu.cn/portainer/portainer-ce | portainer/portainer-ce |
| ImageOptim | docker.mirrors.ustc.edu.cn/hmqgg/image_optim | hmqgg/image_optim |

拉取成功后，再执行脚本部署命令。

如果手动拉取 USTC 源镜像，但也始终提示 `Error response from ...` 无法成功拉取对应镜像，可以考虑更换为官方源（境内下载偏慢）。如果这样做，执行脚本部署命令需要加上 `FORCE_GLO=true`，改为：
```bash
sudo FORCE_GLO=true ./fvtt.sh
```
*更新时同理，在后面添加* `recreate` 变为 `sudo FORCE_GLO=true ./fvtt.sh recreate`。

---

{{% alert title="注意" color="primary" %}}
如果多次提示部署失败，可以使用 `sudo ./fvtt.sh clear` 接着输入两次 `y` 和回车以确认，清空已经完成的所有配置和存储，然后重新尝试全新安装

**当心！如果已经使用此法运行过 FVTT 并有保存好的世界、模组、合集、游戏等文件，请勿使用 `sudo ./fvtt.sh clear` 命令，否则会一并清除所有数据。在如此做之前，请确认你知道你在做什么**
{{% /alert %}}

---

### 部署完成后进行诊断
部署完成后，可能会出现无法访问、文件管理器打不开等异常情况，可以使用脚本进行诊断（版本需要 `v1.4.0+`，如果版本过低可以参见上文[更新脚本](#更新脚本)），执行：
```bash
sudo ./fvtt.sh check
```

运行诊断时将会如下图一般输出结果，正常情况下结果应类似：
![](/images/deployment/docker-script-diagnose.png)

该诊断可以多次运行；根据以上输出的结果，可以进行以下判断：

---

> 以上状态均正常，但是浏览器无法打开 FVTT，*以及无法打开 Web 文件管理器和 Docker 仪表盘（如果选择启用）*

检查服务器防火墙设置，具体可以参见云服务器指南中的[部署完毕后无法访问](../cloud/#部署完毕后无法访问)。

---

> **FoundryVTT 容器状态**显示 `running`，**登录状态**显示 `登录成功`，但是**下载状态**等待许久而始终显示 `未完成`

查看**下载速度**后的最后几行，如果正在下载 FVTT 文件，则此处会显示下载进度的记录。根据最后一行的指示顺序，可以查看到当前的下载状态，最后一列是当前的下载速度。

类似这种结果，代表还在下载：
![](/images/deployment/docker-script-dlspeed.png)

如果**当前下载速度**降低到难以完成下载（通常是在境内服务器直接输入版本号或者回车跳过时），可以考虑删除旧有容器，然后重新部署，并使用[星界投影](../../../modules/#fvtt-软件镜像)镜像的下载地址，加速 FVTT 下载，完整流程基本等于升级的步骤，具体可以参见上文[升级流程](#fvtt-升级更新流程)。

*境内服务器如果不使用镜像（比如镜像下线无法连接时），尽量考虑在白天进行下载，网络连接状况会好一些*。

---

> **FoundryVTT 容器状态**显示 `running`，但是**登录状态**显示 `登录失败`，且**下载状态**始终显示 `未完成`

这种情况代表输入参数时，填写了 FoundryVTT 的用户名和密码，但是用户名/密码错误，导致无法成功登录。

按照升级流程，删除旧有容器，然后重新部署，参见上文[升级流程](#fvtt-升级更新流程)。

需要注意的是，更新过程中在询问**是否直接使用已存储的上次部署使用的配置**的时候，因为上次部署使用的用户名/密码并不正确，所以需要放弃使用：输入 `n` 然后回车，拒绝使用，然后按照正常安装流程输入参数进行。

*如果暂时无法提供正确的 FVTT 用户名/密码，可以在输入用户名和密码时，都回车跳过。但这种情况下，必须要使用可下载镜像地址（比如[星界投影](../../../modules/#fvtt-软件镜像)）或者从 FVTT 官网获取的直链来填写**版本号或下载地址**，否则无法成功下载 FVTT 软件文件，也就无法运行*。

---

> **FoundryVTT 容器状态**显示 `exited`，**可访问性**显示 `unhealthy`，**FoundryVTT 文件状态**却显示 `可以运行`

FVTT 容器意外退出且没有重启，可以重启所有容器，执行：
```bash
sudo ./fvtt.sh restart
```

重启后等待几十秒，如果仍然无法访问可以考虑按照[升级流程](#fvtt-升级更新流程)重新部署。

---

{{% alert title="注意" color="warning" %}}
如果多次提示部署失败或者出现部署后诊断一切正常但仍无法打开，可以使用 `sudo ./fvtt.sh clear` 接着输入两次 `y` 和回车以确认，清空已经完成的所有配置，然后重新尝试安装

但需要注意，如果之前已经通过此法部署并使用过 FVTT，拥有世界、合集、模组等游戏文件，清空会**一并删除所有数据**！在如此做之前，请确定你知道你在做什么
{{% /alert %}}

---

## 脚本额外功能

### 图片自动压缩/优化
> 需要脚本版本 `v1.5.0+`

自动部署脚本中包含自动监控并优化 FVTT 数据目录下图片 `(jpg|png|gif|svg)` 的容器：当有图片加入 `/data` 数据目录下，如果已经部署了优化容器，将会操作图片并进行*有损压缩*，在尽量少损失图片质量的情况下，减小图片的文件体积。

FVTT 安装 MOD 和系统时，会清空文件夹并重新填充内容，故而本容器配置时会跳过 `modules`/`systems` 文件夹下的所有图片。

#### 启动图片优化容器
```bash
sudo ./fvtt.sh do_optim
```

#### 移除图片优化容器
```bash
sudo ./fvtt.sh undo_optim
```

*移除 FVTT 或清空也会移除图片优化容器*。
