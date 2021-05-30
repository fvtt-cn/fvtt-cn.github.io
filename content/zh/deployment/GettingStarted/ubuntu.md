---
title: "Ubuntu 部署 FoundryVTT 教程"
linkTitle: "Ubuntu 部署教程"
weight: 11
type: docs
---

> 本篇教程参考 FVTT 英文社群网站 [Ubuntu VM 部署教程](https://foundry-vtt-community.github.io/wiki/Ubuntu-VM/)

---

## 介绍
Ubuntu 目前逐步取代了其他发行版，取得了新的大量的市场份额，无论是在桌面端还是在服务器端。这篇教程主要讲述如何在安装了 Ubuntu Server 的服务器上部署 Foundry VTT，并使其保持运行并使用反向代理访问。

我们会使用 node 的进程管理工具来保证 Foundry VTT 的实例持续运行，而且提供详尽的统计信息，还包括方便的启动、停止、重启命令，以及服务器重启时自动启动 FVTT 的服务。

如果拥有域名的话，你和玩家们将会从浏览器上输入域名，通过 HTTPS 加密的反向代理访问 FVTT；如果没有使用到域名，也会输入反代后的对应 IP 和端口访问。

本篇教程介绍的部署方式，不会直接让玩家们访问 FVTT 实例暴露出的端口。这样做可以获得一些反代的优势，同时不会有什么显著的缺点，了解反代的优劣势具体可以参考网上一些介绍反向代理的文章。

## 配置服务器环境

Ubuntu 服务器上运行 FVTT，需要配置好运行环境：
- Node.js 运行时，其对应的软件包管理器 npm，以及使用 npm 安装的 pm2（进程管理工具）
- 反向代理软件 Caddy，以及其暴露端口的防火墙（比如 80、443）

首先，假设已经登入了 Ubuntu Server 服务器，并且能够获得 root 权限。

### 第一步：安装 Node.js, npm 和 Unzip

运行下面命令（适用于 Ubuntu，其他 Linux 发行版使用的命令可能不一致）：
```bash
curl -sL https://deb.nodesource.com/setup_14.x | sudo bash -
sudo apt install -y libssl-dev nodejs unzip
```
以上命令会安装 node.js 以及包管理器 npm。使用 `node --version` 和 `npm --version` 来确认安装是否成功以及版本号。

### 第二步：安装 Caddy

运行以下命令将会安装 Caddy：
```bash
echo "deb [trusted=yes] https://apt.fury.io/caddy/ /" | sudo tee -a /etc/apt/sources.list.d/caddy-fury.list
sudo apt update
sudo apt install caddy
```
以上命令将会同时创建用于启动 Caddy 的 Linux 用户和用户组，接下来启用 Caddy 服务：
```bash
sudo systemctl enable caddy
```

### 第三步：安装 Node.js 进程管理工具：pm2
使用刚才安装的 npm，全局安装 pm2：
```bash
sudo npm install -g pm2
```

### 第四步：下载 FVTT 并测试
创建 FVTT 使用的目录并切换：
```bash
mkdir /tmp/foundrydata && mkdir ~/foundrydata
mkdir ~/foundry && cd ~/foundry
pwd
```
`pwd` 命令输出结果应当是 `/home/[XXX]/foundry`，`[XXX]` 是登录服务器使用的 Linux 用户名。

从官网上获取 FVTT 下载链接（Linux），在服务器上运行：
```bash
wget "https://foundryvtt.s3-us-west-2.amazonaws.com/releases/[Key]/FoundryVirtualTabletop-linux-x64.zip"
```
**后面的这段链接替换为从官网处复制到的链接**。

下载完毕后，解压 FVTT（如果文件名不一致，更换为下载保存的 FVTT 压缩包文件名）：
```bash
unzip FoundryVirtualTabletop-linux-x64.zip
rm FoundryVirtualTabletop-linux-x64.zip
```

然后测试确认 FVTT 是否能够正常运行：`node $HOME/foundry/resources/app/main.js --port=62621 --dataPath=/tmp/foundrydata`：
```bash
FoundryVTT | 2020-07-06 07:48:44 | [info] Foundry Virtual Tabletop - Version 0.6.5
FoundryVTT | 2020-07-06 07:48:44 | [info] Running on Node.js - Version 12.18.2
FoundryVTT | 2020-07-06 07:48:44 | [info] Application Options:
{
  "port": 62621,
  "dataPath": "/tmp/foundrydata",
  "upnp": false,
  "fullscreen": false,
  "hostname": null,
  "routePrefix": null,
  "sslCert": null,
  "sslKey": null,
  "awsConfig": null,
  "proxySSL": false,
  "proxyPort": null
}
```
如果显示类似于上述的输出结果并且没有显示什么错误，证明 FVTT 已经可以正常运行了！

然后按下 <kbd>Ctrl</kbd>+<kbd>C</kbd> 直接杀掉用来测试的 FVTT 进程，准备进行下一步。

## pm2 运行 FVTT
需要的运行环境都已经配置完毕，接下来需要将 pm2 设置为随系统启动，并让 pm2 管理和运行 FVTT 进程。

### pm2 配置为系统服务
首先我们要将 pm2 设置为随服务器开机启动。可以使用 pm2 自带的服务文件生成，对于 Ubuntu 系统，执行：`pm2 startup`，结果如下：
```
[PM2] Init System found: systemd
[PM2] To setup the Startup Script, copy/paste the following command:
sudo env PATH=$PATH:/usr/bin /usr/lib/node_modules/pm2/bin/pm2 startup systemd -u [XXX] --hp /home/[XXX]
```
它生成了一个命令，即`sudo env PATH=$PATH:/usr/bin /usr/lib/node_modules/pm2/bin/pm2 startup systemd -u [XXX] --hp /home/[XXX]`这一部分，其中 `[XXX]` 会被自动替换为当前登录使用的用户。复制输出的这段命令（而非从网页上复制）并且执行：

> 如果使用 root 执行 `pm2 startup` 命令，则会直接配置为系统服务，不需要复制粘贴命令执行

```bash
[PM2] [v] Command successfully executed.
```
如果输出结果中含有以上这段，基本上 pm2 已经被配置为系统服务。

### pm2 启动 FVTT 并持续运行
现在需要使用 pm2 启动 FVTT，并且让 pm2 记住需要保持 FVTT 的运行，如此就可以让 pm2 在每次服务器重启时都会跟随启动，不需要手动。

使用 pm2 启动 FVTT，还是一样 `[xxx]` 替换为登录服务器使用的用户名：
```bash
pm2 start $HOME/foundry/resources/app/main.js --name foundry -- --port=8080 --dataPath=$HOME/foundrydata
```

> 端口号`8080`可以调整，但是后续的端口号都需要对应修改

运行完毕后，使用 `pm2 list` 来查看 pm2 正在管理的进程实例：
```bash
[PM2] Starting /home/[XXX]/foundry/resources/app/main.js in fork_mode (1 instance)
[PM2] Done.
┌──────────┬────┬─────────┬──────┬───────┬────────┬─────────┬────────┬─────┬───────────┬────────┬──────────┐
│ App name │ id │ version │ mode │ pid   │ status │ restart │ uptime │ cpu │ mem       │ user   │ watching │
├──────────┼────┼─────────┼──────┼───────┼────────┼─────────┼────────┼─────┼───────────┼────────┼──────────┤
│ foundry  │ 0  │ 0.6.5   │ fork │ 31130 │ online │ 0       │ 0s     │ 0%  │ 8.9 MB    │ [XXX]  │ disabled │
└──────────┴────┴─────────┴──────┴───────┴────────┴─────────┴────────┴─────┴───────────┴────────┴──────────┘
 Use `pm2 show <id|name>` to get more details about an app
```

pm2 会展示如上所示的列表，并且展示大量有用的信息，比如是否运行中，进程重启次数，运行时间， CPU 使用率和内存使用率等等。使用 `pm2 show foundry` 可以展示更详尽的关于启动的 FVTT 进程的信息。

接下来使用 `pm2 save`，即可保存配置，令 pm2 记住对 FVTT 进程的管理。

- `pm2 start foundry` 启动 FVTT
- `pm2 stop foundry` 停止 FVTT
- `pm2 restart foundry` 重启 FVTT
- `pm2 delete foundry` 删除 FVTT 的管理项，不再使用 pm2 管理

事实上，此处如果 8080 端口防火墙已放行，就应该可以访问了。在自己的浏览器上输入 `http://[服务器IP]:8080` 来尝试访问，防火墙放行即可见到 FVTT 的界面；如果不能访问，是好事，因为我们不想开放这个端口。

## 设置反向代理
相反，用户应该使用反代来连接，而使用 Caddy 可以简化配置反代的流程。而且，如果用到域名，使用 Caddy 配置 HTTPS 将非常简单，它内置申请 LetsEncrypt 的证书并自动更新的方法，无需额外配置。

{{% alert title="可选" color="primary" %}}
**如果使用域名，需要将域名[绑定](../domain-name/#绑定子域名)上**

接下来使用到的域名都假定为 `my.foundry.com`，记得替换为自己的域名
{{% /alert %}}

执行 `sudo nano /etc/caddy/Caddyfile`，将会开始编辑 Caddy 的配置文件。使用域名和不使用域名基本配置文件的内容都近似，在命令进入的 nano 界面中，输入以下内容，根据使用域名与否来区分：

- 不使用域名
  ```
  :80 {
      reverse_proxy localhost:8080 {
          header_up Host {host}
          header_up X-Real-IP {remote_host}
          header_up X-Forwarded-For {remote_host}
          header_up X-Forwarded-Proto {scheme}
      }
  }
  ```
- 使用域名
  ```
  my.foundry.com {
      reverse_proxy localhost:8080 {
          header_up Host {host}
          header_up X-Real-IP {remote_host}
          header_up X-Forwarded-For {remote_host}
          header_up X-Forwarded-Proto {scheme}
      }
  }
  ```

输入或者粘贴完毕后，按下 <kbd>Ctrl</kbd>+<kbd>X</kbd>，接着按 <kbd>y</kbd> 并且回车，确认保存文件。

然后启动已经配置好的 Caddy 服务即可：
```bash
sudo systemctl start caddy
```

等待一段时间后，通过你设置的域名或者服务器 IP 地址，即可访问已经部署完毕的 FVTT，尽情使用吧。

{{% alert title="注意" color="warning" %}}
如果无法访问，检查是否开启服务器防火墙的 80/443 端口，这两个端口需要打开
{{% /alert %}}
