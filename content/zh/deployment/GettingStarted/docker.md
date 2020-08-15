---
title: "Docker 部署 FoundryVTT 教程"
linkTitle: "Docker 部署教程"
weight: 21
type: docs
---

Foundry VTT 目前拥有若干个来自不同用户实现的 Dockerfile，这些实现中，较为推荐 [felddy/foundryvtt](https://github.com/felddy/foundryvtt-docker) 的镜像。

## 介绍

目前可以通过中文社区的自动脚本进行容器化部署，也可以手动启动容器。

在全新购买的云服务器上使用自动脚本进行部署，最少只需要输入 Foundry VTT 站点上的用户名和密码即可完成，同时脚本也包含了后续更新、维护的方法。

### 使用 Docker 的优点
- 无需关心系统环境：只要是主流发行版的 Linux，甚至于 Windows 服务器，只要安装了 Docker，即和其他用户的部署行为一致，难以因系统环境问题出错
- 直接使用 Docker 的后台管理：Docker 自己会监控容器内运行的进程状态，如果意外情况导致 FVTT 崩溃，可以不需要 pm2 等进程监控工具即可自动重启、还原
- 目前主流的 FVTT 的 Docker 镜像包含内置的自动获取下载地址和授权的脚本，可以简化部署流程
- ...

### 使用 Docker 的缺点
- Docker 本身需要安装，同时拉取镜像、容器本身都消耗了一定冗余的存储空间
- 如果使用卷挂载，直接访问 FVTT 的数据文件会变得略困难（可以通过 Docker 启动其他容器简化文件访问）
- 目前主流的 FVTT 的 Docker 镜像升级时需要重建，而不能通过 FVTT 内置更新
- ...

## 安装流程

### 自动部署脚本（中文社区） 使用教程
1. 首先，假设已经登入了 Linux 服务器，并且能够获得 root 权限
2. 下载脚本
    ```bash
    wget https://gitee.com/mitchx7/FoundryDeploy/raw/master/fvtt.sh && sudo chmod +x fvtt.sh
    ```
3. 运行脚本，进行安装
    ```bash
    sudo ./fvtt.sh
    ```
    > 安装时会检查是否能获取 root 权限，并且如果没有安装 Docker 会自动安装 Docker
    > 
    > 境内服务器下载镜像时会等待较长时间，可以耐心等待

    然后脚本会提示输入参数，每条参数输入完成后回车即可，参数包含（✅：必需 | ⭕：可选）：
    ![](/images/deployment/docker-script-input.png)
    - ✅ FVTT 账号
    - ✅ FVTT 密码
    - ⭕ 版本号
        - 直接回车跳过输入版本号，使用最新稳定版即可
    - ⭕ 自定义管理密码
        - 安装完成后，如果设置了这个密码，需要使用密码才能管理 FVTT，推荐进行设置；回车跳过就不设置密码
    - ⭕ FVTT 绑定域名
        - 如果为这台服务器绑定了给 FVTT 使用的域名，可以输入，否则直接回车跳过
    - ⭕ 使用 Web 文件管理器
        - 为了管理文件方便，建议使用。直接回车跳过设置，默认会使用；如果不使用输入`n`再回车
    - ⭕ 如果使用 Web 文件管理器，可以绑定域名
        - 如果需要给 Web 文件管理器绑定域名进行使用，可以输入，否则直接回车跳过

    确认输入好的参数之后，便可以回车等待部署完成

4. 部署完成后，脚本会提示如何访问 FVTT，如图所示：
![](/images/deployment/docker-script-result.png)

> 如上图所示的部署完毕后，如果还是不能访问 FVTT，请参见 FAQ 中的[无法连接](../../../tutorial/faq#为什么显示安装成功后仍然无法连接-foundryvtt)的问题

{{% alert title="Tips" color="info" %}}
升级 FVTT 或者修改启动配置，请参见[脚本仓库](https://github.com/fvtt-cn/FoundryDeploy)中的文档
{{% /alert %}}

---

### 手动运行 FVTT 容器教程
*手动更适合希望自定义部署步骤，并且不嫌麻烦的用户*

{{% alert title="注意" color="warning" %}}
**手动运行和自动脚本故意设计为会发生冲突，请只选择其中一种方式部署**
{{% /alert %}}


1. 首先，假设已经登入了 Linux 服务器，并且能够获得 root 权限
2. 确保 Docker 已安装，执行：
    ```bash
    curl -fsSL https://get.docker.com | sudo sh
    ```
    等待执行完毕就可以进入下一步
3. 先创建一个 Docker 网桥，执行：
    ```bash
    sudo docker network create caddy_network
    ```
    修改下面的命令，然后复制粘贴后执行
    ```bash
    sudo docker run -d \
    --name=fvtt \
    --restart=unless-stopped \
    --network=caddy_network \
    -v fvtt_data:/data \
    -e FOUNDRY_USERNAME='FVTT官网用户名' \
    -e FOUNDRY_PASSWORD='FVTT官网密码' \
    -e FOUNDRY_ADMIN_KEY='设置一个部署完成后管理FVTT的密码' \
    felddy/foundryvtt:release
    ```

    > 需要注意的是，之后访问 FVTT 不要使用任何服务器设置功能来更改选项，会被现在的设置直接覆盖（包括 FVTT 管理密码，但保存的世界、系统、MOD 等等不受影响）
    > 
    > 并且已经屏蔽自动升级功能，需要升级或者修改服务器其他设置的话参见下文（访问域名、端口等修改 Caddy 配置）

4. 启动 Caddy
    使用 Caddy 作为反代，可以有效降低的 HTTPS 的部署成本，毕竟全自动申请免费的 LetsEncrypt 的 HTTPS 证书且自动更新，无需额外配置

    先绑定好域名，假设绑定到这台机器的域名的是 `my.fvtt.com`

    那么执行：
    ```bash
    nano Caddyfile
    ```

    然后会进入一个编辑文件的界面，在这个界面里，写下 Caddy 的配置文件，可以修改 `my.fvtt.com` 后直接复制粘贴，示例如下：
    ```Caddyfile
    my.fvtt.com <= 修改这个 {
        reverse_proxy fvtt:30000 {
            header_up Host {host}
            header_up X-Real-IP {remote_host}
            header_up X-Forwarded-For {remote_host}
            header_up X-Forwarded-Proto {scheme}
        }
    }
    ```

    无论是否有域名，输入或者粘贴完毕后，按 <kbd>Ctrl+X</kbd>，会提示是否覆写编辑内容，按 <kbd>y</kbd> 并且回车确认保存

    接着启动 Caddy 进行反代，执行：
    ```bash
    sudo docker run -d \
    --name=caddy \
    --restart=unless-stopped \
    --network=caddy_network \
    -p 80:80 \
    -p 443:443 \
    -p 8080:8080 \
    -v cdata:/data \
    -v $PWD/Caddyfile:/etc/caddy/Caddyfile \
    caddy
    ```

    启动后等待不到几秒后（大部分情况下），就应该已经可以访问 FVTT 了，直接在浏览器输入绑定的域名就可以

5. 启动 FileBrowser（可选）

    我们都知道 FoundryVTT 目前的文件管理非常的麻，比如暂不支持删除、移动等等必需功能

    如果你本来用 Windows Server 部署，那么你还可以远程桌面来试图管理（其实也不是特别方便），如果你使用 Linux 并且像上面一样使用 Docker 来部署，那么就不要考虑直接在用命令移动、删除文件了

    为了解决这个问题，最好的方法是启动一个 Web 文件管理器的 Docker 容器，让它挂载我们的 FVTT 的数据文件，然后我们用浏览器打开这个 Web 文件管理器就可以方便地管理文件

    这里推荐使用基于 Golang 的 FileBrowser，使用足够方便：
    ```bash
    sudo docker run -d \
    --name=filebrowser \
    --restart=unless-stopped \
    --network=caddy_network \
    -v fvtt_data:/srv \
    filebrowser/filebrowser
    ```

    建议再给这台运行 FVTT 的机器绑定一个新的域名用于访问文件管理器，例如 `file.fvtt.com`，然后修改 Caddyfile 让 Caddy 也反代 FileBrowser，执行：
    ```bash
    nano Caddyfile
    ```

    然后用方向键调整到末尾，可以修改 `file.fvtt.com` 后直接粘贴到文件末尾，示例如下：
    ```Caddyfile
    file.fvtt.com {
        reverse_proxy filebrowser:80 {
            header_up Host {host}
            header_up X-Real-IP {remote_host}
            header_up X-Forwarded-For {remote_host}
            header_up X-Forwarded-Proto {scheme}
        }
    }
    ```

    同样在输入或者粘贴完毕后，按 <kbd>Ctrl+X</kbd> 然后按 <kbd>y</kbd> 并且回车确认保存

    接着，重启 Caddy 以使其启用对 FileBrowser 的反代：
    ```bash
    sudo docker restart caddy
    ```

6. 升级 FVTT 版本或者修改配置
    先移除正在运行的 FVTT 的容器，执行：
    ```bash
    sudo docker rm -f fvtt
    ```

    并且更新 FVTT 镜像，执行：
    ```bash
    sudo docker pull felddy/foundryvtt:release
    ```

    然后执行和启动时一模一样的命令：
    ```bash
    sudo docker run -d \
    --name=fvtt \
    --restart=unless-stopped \
    --network=caddy_network \
    -v fvtt_data:/data \
    -e FOUNDRY_USERNAME='FVTT官网用户名' \
    -e FOUNDRY_USERNAME='FVTT官网密码' \
    -e FOUNDRY_ADMIN_KEY='设置一个部署完成后管理FVTT的密码' \
    felddy/foundryvtt:release
    ```
