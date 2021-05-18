---
title: "导入与导出数据"
linkTitle: "导入与导出数据"
weight: 22
type: docs
---
有时候，你想要导出你的数据分享给其他 GM，亦或者是为自己制作备份。

## Windows 版本操作

### 世界（world）

（在本地/网络服务器的文件管理器中）
要导出世界，找到你的数据目录，例如 `C:/FoundryVTTdata/Data/World/`，找到你所创建的世界，复制后粘贴出来即可。
导入预设世界（比如别人做好的模组世界）时，把文件夹拷贝到数据目录即可。

### 角色、物品、日志、随机表（Actor、Item、Journal、RollTable）

（在 FVTT 中）
创建一个新角色，右键角色，选择`导出数据`，保存 `json` 文件。
在需要导入的 FVTT 世界中，新建一个角色（随意命名即可），右键`导入数据`，选择你所保存出来的 `json` 文件，导入。
其他类型（物品、日志、随机表）同样。

### 合集包（Companion）

（在FVTT中）
新建一个合集包（需要使用英文字符命名，例如 `heroes`），将你所需要转移的条目拖动到此合集。

（在本地/网络服务器的文件管理器中）
打开数据目录（例如 `C:/FoundryVTTdata/Data/World/你的世界/packs`），你可以找到刚才生成的 `.db` 文件。(例如 `heroes.db`)
将此.db文件复制并粘贴到你的备份目录即可。

#### 当你需要导入此合集包时：
打开数据目录（例如 `C:/FoundryVTTdata/Data/World/你的世界/packs`），如果没有 packs，新建一个。
将你需要导入的合集包数据文件（例如 `heroes.db`）复制粘贴到此目录。
使用文本编辑器（比如记事本）编辑你的世界中 `world.json`。
修改/添加以下字段。
```json
  "packs": [
    {
      "label": "新手团的PC角色",    // 显示在合集中的标题名
      "entity": "Actor",	  // 实体类型，只能填已有实体名不然会报错（例：Actor, Item, JournalEntry, RollTable, Macro)
      "name": "heroes",           // 名称
      "path": "packs/heroes.db",  // 实际路径
      "system": "dnd5e",          // 使用的规则系统，可在 system 中查看对应的 system.json 确定
      "package": "world",
      "absPath": "D:\\TRPG-F\\Data\\worlds\\test\\packs\\heroes.db"  // 绝对路径，根据你的服务器情况修改
    }
  ],
```

如果导入多个 db ，则为每个 db 单独写一段。注意每个 `}` 后面需要有一个 `,` 符号，最后一个 `}` 不能有 `,` 符号。比如：
```json
  "packs": [
    {
      "label": "新手团的PC角色",
      "entity": "Actor",
      "name": "",
      "path": "packs/heroes.db",
      "system": "dnd5e",
      "package": "world",
      "absPath": "D:\\TRPG-F\\Data\\worlds\\test\\packs\\heroes.db"
    },	// 注意此处有符号(,)英文半角
    {
      "label": "新手团的PC角色2",
      "entity": "Actor",
      "name": "",
      "path": "packs/heros2.db",
      "system": "dnd5e",
      "package": "world",
      "absPath": "D:\\TRPG-F\\Data\\worlds\\test\\packs\\heroes2.db"
    } // 注意此处没有符号
  ],
```

### 制作即插即用式合集包（本地MOD）

如果你觉得这么做太过于麻烦，也可以考虑以MOD的形式来保存与分享你制作的合集包。

首先，在任意位置新建一个文件夹，命名为 `my_data`。
将你创建的合集包数据（比如 `heroes.db`）复制到此文件夹下。
新建一个文本文档，写入以下数据：
```bash
{
    "name": "my_data",	     // 此处需要与你的文件夹同名，仅限英文
    "title": "我的合集包数据",	// 在 FVTT mod 管理器中显示的合集包标题
    "description": "我的合集包数据，包含几个适合新手的角色，分享给大家", // 在 FVTT mod 管理器中显示的合集包描述
    "version": "0.1.0",	                                         // 随便填
    "minimumCoreVersion" : "0.7.0",                              // 支持最低核心版本，建议 0.7.0
    "author": "我",      // 作者名
    "scripts": [],       // 留空
    "styles": [],        // 留空
    "packs": [{
        "name": "我的英雄",     // 在FVTT世界中显示的合集包标题
	"label": "我的英雄",    // 在FVTT世界中显示的合集包标签
	"path": "heroes.db",   // 数据文件名称
	"module": "my_data",   // 此处需要与你的文件夹同名，仅限英文
	"entity": "Actor"      // 合集包类型，必须是已存在的指定类型，看上文
    }],
    "url": "https://github.com/my_data",	// 随意填
    "manifest": "",	                        // 留空
    "download": ""	                        // 留空
}
```

包含多个 db 文件时，在 packs 中增加一段即可：(注意每个段末的`},{`之间用`,`分开，末尾段落无`,`符号）

```
"packs": [{
		"name": "我的英雄",	
		"label": "我的英雄",	
		"path": "heroes.db",	
		"module": "my_data",
		"entity": "Actor"
	},{
		"name": "我的法术",	
		"label": "我的法术",	
		"path": "spells.db",	
		"module": "my_data",
		"entity": "Item"
	}],
```

将此文本文档重命名为 `module.json`，然后你就可以将此文件夹打包发送给其他人了。
安装方式如同基本的手动 MOD 安装，在开启世界后在 MOD 设置中启用此 MOD 即可。
