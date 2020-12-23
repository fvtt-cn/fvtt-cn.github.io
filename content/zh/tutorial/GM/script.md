---
title: "常用脚本"
linkTitle: "常用脚本"
weight: 21
type: docs
---

作为服务器管理者，GM 可以使用一些浏览器控制台上的脚本，方便自己使用。

---

### 导出/导入 MOD 列表
在设置界面，即 `安装 MOD` 界面，按下 F12 弹出开发者工具，切换到控制台，输入脚本并回车。

以下脚本可以用来导出/导入自己安装的所有 MOD 的下载地址方便备份服务器来安装，导出后也可以分享给其他人用于快速分享所有 MOD。

#### 导出：

```javascript
Array.from(this.game.modules.values()).map(v => v.data.manifest).join();
```

然后复制输出的那段文本，即所有已安装 MOD 的下载地址。

#### 导入：

```javascript
await Promise.all("https://fvtt.io/foundry/module.json,...（替换上面导出的文本）".split(',').map(m => SetupConfiguration.installPackage({type: "module", manifest: m})));
```

#### 只导出 MOD 名称：

```javascript
Array.from(this.game.modules.values()).map(v => v.data.title).join('\n');
```

### 一键开启所有 MOD
在世界中，管理 MOD 界面，也就是勾选 MOD 开启和关闭的窗口打开之后，按下 F12 弹出开发者工具，切换到控制台，输入脚本并回车。

```javascript
document.querySelectorAll("input[type=checkbox]").forEach(x => x.checked = true);
```

然后点击保存即可。