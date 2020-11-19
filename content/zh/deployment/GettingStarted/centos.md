---
title: "CentOS 部署 FoundryVTT 教程"
linkTitle: "CentOS 部署教程"
weight: 16
type: docs
---

> 这里介绍[雙月ひめは的 CentOS 教程](https://www.peatsuki.com/008.html)以及其[一键部署脚本教程](https://www.peatsuki.com/010.html)。

---

## 脚本部署

你需要一个CentOS7.x版本的 linux虚拟机，然后执行如下命令：

```bash
sudo su root
cd
wget https://sh.peatsuki.com/fvtt/InstallFVTT.sh --no-check-certificate
sh InstallFVTT.sh
```
~~[官网地址](https://foundryvtt.com/) 登录以购买的账户以获取下载链接。~~
现无需输入官网地址，已经重定向到镜像，但仍需要登入官网购买licence以便激活系统

安装过程中需要中输入非【P】的任意字符选择 `以nohup进行后台运行`。

若需要使用 PM2 作为后台监视程序，建议有足够的OPS知识后进行选择。

如需上传文件，需要设置 root 账号密码后使用 SFTP 工具上传，例如 WinSCP 等。

---
如果因为设置密码造成的系统重启，则需要在远程连接中执行如下内容：（执行后需要等待2-3分钟系统运行即可访问）
```bash
sudo su root
cd
sh startfvtt.sh
```

### 使用脚本在阿里云 CentOS 机器上部署的教程视频

{{< bilibili BV1Q54y1v7FX >}}

## 手动部署

### 0x00 目标服务器
阿里云轻量服务器是一个物美价廉的香港服务器。

目前定价为 1U1G=24 元、1U2G=34 元。（截至 2020/7/1 日）

峰值带宽为 30M，流量为 1T-2T 左右。已经足够当我们的服务器使用。

请注意：由于非大陆地域的虚拟主机提供国际带宽，如在中国大陆使用，会有较大的网络延迟。

### 0x01 我的服务器状况
我的服务器上已经做了 apache+php+wordpress，并且配置了全域 SSL 和加入 HSTS Preload List。

经过考虑，我让 FoundryVTT 和这个 wordpress 博客挤一挤。以下制作代码建立在我的环境之下。

阿里云自带的 WordPress 4.8.1 或许能提供类似环境（不保证）

### 0x02 安装node.js环境（来源于官方指导）
依次运行以下语句
```
sudo yum install -y openssl-devel
curl --silent --location https://rpm.nodesource.com/setup_12.x | sudo bash -
sudo yum install -y nodejs
```

### 0x03 创建对应文件夹并下载FoundryVTT运作档（来源于官方指导）
依次运行以下语句，其中斜线部分需要去你的FoundrtVTT授权页面点击node.js后方的链条按钮获得临时下载地址，并替换斜体部分。
<pre>
cd $HOME
mkdir foundryvtt
mkdir foundrydata
cd foundryvtt
wget -O foundryvtt.zip "<em>https://foundryvtt.s3.amazonaws.com/releases/0.6.4/foundryvtt-0.6.4.zip</em>"
unzip foundryvtt.zip
</pre>

### 0x04 使用直接运行的方式测试FoundrtVTT（来源于官方指导）
运行以下语句并访问你的服务器公网地址测试，例如：`http://127.0.0.1:30000`
```
node resources/app/main.js --dataPath=$HOME/foundrydata
```

### 0x05 使用PM2创建node.js值守进程（来源于社区指导）
依次运行以下语句，安装pm2并创建值守进程
```
cd $home
npm install -g pm2
pm2 start foundrtvtt/resources/app/main.js
```

### 0x06 安装SSL证书
```
cd /foundrydata
mkdir ssl
```
然后将证书的 crt、key 文件拷贝到 ssl 文件夹下。（建议采用 FTP 方式或者 SFTP 方式）

打开 FoundryVTT，在 Configuration 中修改以下内容（斜体部分需要根据实际情况修改）
- UserDataPath修改为 *`/root/foundrydata`*
- 填写SSL Certifilcate *`/root/foundrydata/ssl/yourdomain.crt`*
- 填写SSL Private Key *`/root/foundrydata/ssl/yourdomain.key`*

然后点击 Save Changes 保存配置文件

### 0x07配置apache实现反向代理
修改 `/usr/local/apache/conf/httpd.conf` 的内容，添加以下字段：
```
LoadModule proxy_module modules/mod_proxy.so
LoadModule proxy_http_module modules/mod_proxy_http.so
LoadModule proxy_connect_module modules/mod_proxy_connect.so
LoadModule proxy_wstunnel_module modules/mod_proxy_wstunnel.so
LoadModule proxy_ftp_module modules/mod_proxy_ftp.so
```
修改 `/usr/local/apache/conf/extra/httpd-ssl.conf` 文件，添加以下字段，其中斜下划线部分需要根据实际情况修改：
<pre>
&lt;VirtualHost *:443&gt;
ServerName <em><u>yourdomain.com</u></em>
ServerAlias <em><u>yourdomain.com</u></em>
RewriteEngine On
RewriteCond %{REQUEST_URI} ^/socket.io [NC]
RewriteCond %{QUERY_STRING} transport=websocket [NC]
RewriteRule /(.*) <em><u>wss://yourdomain.com:30000/$1</u></em> [P,L]
SSLEngine On
SSLProxyEngine on
SSLCertificateFile <em><u>/root/foundrydata/ssl/yourdomain.crt</u></em>
SSLCertificateKeyFile <em><u>/root/foundrydata/ssl/yourdomain.key</u></em>
SSLCertificateChainFile <em><u>/root/foundrydata/ssl/yourdomain_ca.crt</u></em>
Options FollowSymLinks
AllowOverride All
Require all granted
ProxyRequests Off
ProxyPass / "<em><u>https://yourdomain.com:30000/</u></em>"
ProxyPassReverse / "<em><u>https://yourdomain.com:30000/</u></em>"
&lt;/VirtualHost&gt;
</pre>

最后重启apache：
`/usr/local/apache/bin/apachectl restart`
