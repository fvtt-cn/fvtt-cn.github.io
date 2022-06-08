---
title: "使用 Babele 制作合集包汉化"
linkTitle: "合集包汉化"
weight: 23
type: docs
---

在本教程中，你将学会如何使用 `Babele` 为 `Foundry VTT` 中的任意合集包（`compendium`）制作翻译类 MOD。

---

## 什么是 `Babele`？如何使用它？

援引自其 [Gitlab 主页](https://gitlab.com/riccisi/foundryvtt-babele)：

> `Babele` 是一个用于运行时翻译合集包的模组。
>
> `Babele` 从 `JSON` 格式的配置文件中获取翻译并将其应用到原始合集包中，覆盖内存中实体的映射属性而不改变原始内容，这样就没有必要仅出于翻译目的保留合集包副本的内容。

## 翻译文件

截止至本教程写成，`Babele` 的最新版本号为 `2.2.5`，并支持导出两种格式的翻译文件，即传统（legacy）和兼容（compatible）。

导出翻译文件的方法为：
  1. 打开合集包
  2. 在标题栏上可以发现 `translate` 按钮，若有启用 `mega` 汉化包则为 `翻译`
  3. 点击按钮，选择保存位置
  4. 开始翻译

一般情况下，默认导出的翻译文件已足够使用，但如果需要更深层次的翻译，请继续阅读。

#### 传统格式

传统格式结构如下：
```json
{
    "label": "翻译后的合集包名称",
    "entries": [
        { "id": "用以匹配的物品名称或id", "name": "用于显示的翻译后名称", "description": "翻译后的物品描述" }
    ]
}
```

各字段的含义均已列出，其中 `id` 字段支持使用英文原文以及在 `.db` 文件中列出的 `id`。

在该格式中，`entries`声明为数组。

#### 兼容格式

兼容格式结构如下：
```json
{
    "label": "翻译后的合集包名称",
    "entries": {
       "用以匹配的物品名称或id": { 
           "name": "用于显示的翻译后名称", 
           "description": "翻译后的物品描述" 
       }
    }
}
```

兼容格式是用于在如 `Weblate` 之类的在线翻译平台使用的，各字段含义均已列出。

在该格式中，`entries` 不再声明为数组，而是声明为 `JSON` 对象。

## 翻译映射

将翻译应用于实体的规则称为**翻译映射**（`mapping`），可以使用 `JSON` 定义，其中属性为定义的规则，而其对应的值即为将要翻译的实体数据的路径，如下所示：
```json
{
    "description": "data.details.biography.value"
}
```

在默认情况下，`Babele` 预先定义好的各类型实体映射如下：
```json
{
  "Actor": {
      "name": "name",
      "description": "data.details.biography.value",
      "items": {
          "path": "items",
          "converter": "fromPack"
      },
      "tokenName": {
          "path": "token.name",
          "converter": "name"
      }
  },
  "Item": {
      "name": "name",
      "description": "data.description.value"
  },
  "JournalEntry": {
      "name": "name",
      "description": "content"
  },
  "RollTable": {
      "name": "name",
      "description": "description",
      "results": {
          "path": "results",
          "converter": "tableResults"
      }
  }
}
```

其中 `converter` 为特殊定义的函数，该函数允许动态更改原始数据，而不是使用静态文本。详见下文。

若想添加自定义的映射规则，则可在翻译文件中添加 `mapping` 字段，如下例：
```json
{
    "label": "SWADE奇术",
    "mapping": {
        "description": "data.description",
        "range": "data.range",
        "rank": "data.rank",
        "duration": "data.duration",
        "trapping": "data.trapping",
        "pp": "data.pp"
    },
    "entries": {
        "Zombie": {
            "name": "行尸",
            "description": "描述文本",
            "rank": "老练",
            "range": "聪慧",
            "duration": "一小时",
            "trapping": "在尸体上铭刻符号，投掷骨头，坟场，“皮革”书。"
        }
    }
}
```

若想知道如何获取需要添加进去的字段，请使用[Data Inspector](https://gitlab.com/koboldworks/agnostic/data-inspector)。

## `Converter` 函数

`converter` 是一种允许动态更改原始数据的函数，而非使用静态文本进行更改。

`Converter` 函数需定义为如下形式：

```javascript
function converter(value, translations) {
    // ...
}
```

其中：

- **value**：原始未翻译的属性值。
- **translations**：翻译文件的完整实体列表。

`Babele` 内置三个 `Converter` 函数，分别为 `name`，`fromPack` 和 `tableResults`。

`name` 始终返回实际实体的翻译名称，在 `Actor` 默认映射中用于将角色的已翻译名称与指示物名称“同步”。

`fromPack` 允许你覆盖翻译不完全的合集包中的任何物品，比如在翻译文件中某些物品存在而另一些物品不存在。要覆盖（或添加）已有的物品翻译，只需在 `items` 下的翻译中提供它们：
```json
{
  "label": "Mostri (SRD)",
  "entries": [{
      "id": "Acolyte",
      "name": "Accolito",
      "description": "...",
      "items": [{
        "id": "Club",
        "name": "Randello dell'accolito",
        "description": "Il randello dell'accolito è un'arma molto potente..."
      }]
    }
  ]
}
```

此处使用 `Babele` 主页提供的例子说明，在上面的示例中，`Club` 物品将被提供的自定义翻译覆盖，而非使用物品合集包中提供的翻译。其他未被覆盖的物品将通过从相关合集包（如果有）中获取来进行翻译。

兼容格式如下：
```json
{
  "label": "Mostri (SRD)",
  "entries": {
    "Acolyte": {
      "name": "Accolito",
      "description": "...",
      "items": {
        "Club": {
          "name": "Randello dell'accolito",
          "description": "Il randello dell'accolito è un'arma molto potente..."
        }
      }
    } 
  }
}
```

若随机表结果是来自已翻译合集包的“实体”，则 `tableResult` 会自动翻译随机表投骰结果，而文本类型元素的翻译可以在翻译文件中的“results”属性下提供，映射为 `键-值` 对，其中键对应于关联的“范围”。

若要翻译如下形式的随机表：

![](/images/tutorial/gm/compendium-translate/1654573371199.png)

则翻译文件中的内容需为如下形式：
```json
{
  "label": "Tabelle di prova",
  "entries": [
    {
      "id": "Test",
      "name": "Prova",
      "description": "Descrizione tradotta",
      "results": {
        "1-1": "Testo tradotto"
      }
    }
  ]
}
```

如此，得到的翻译结果如下：

![](/images/tutorial/gm/compendium-translate/1654573477936.png)

如你所见，与“1-1”范围关联的文本已通过从文件中检索内容进行翻译，而实体则通过从相关的已翻译合集包中检索其各自的翻译文本来自动翻译。

当然，你也可以自定义 `converter` 函数。

自定义函数的例子如下：
```javascript
Babele.get().registerConverters({
    "lbToKg": (value) => { return parseInt(value)/2 }
});

```

使用 `Babele.get().registerConverters()` 注册 `converter` 函数，如上，我们定义了一个名为 `lbTokg` 的 `converter` 函数，并可在 `mapping` 中使用。

```javascript
{  
    "label": "Oggetti (SRD)",
    "mapping": {
        "flavor": "data.chatFlavor",
        "weight": {
            "path": "data.weight",
            "converter": "lbToKg"
        }
    }
}
```

如此，物品的重量即会被自动从磅转换为千克。

## 模组制作

要将你的合集包翻译制作为模组，请参考如下步骤：

可以使用模组[模板](https://github.com/League-of-Foundry-Developers/FoundryVTT-Module-Template)。

创建 `babele-register.js` 脚本文件，并写入以下内容：

```javascript
Hooks.on('init', () => {
    if(typeof Babele !== 'undefined') {
        Babele.get().register({
            module: '模组名称',
            lang: '语言',
            dir: '目录'
        });
    }
});
```

其中：

- `module`：模组的名称，必须与 `module.json` 中的模组名称相同
- `lang`：翻译文件的语言，即将fvtt的语言设置为与此项相同时，翻译才会被应用，对于中文，请使用 `cn`
- `dir`：翻译文件在模组中存放的目录名称

将该脚本文件添加至 `module.json` 中的 `scripts` 字段中，并将翻译文件放入上述路径

## 范例模组
- [fvtt-cn/pf2e_compendium_chn: Pathfinder 2e 合集包汉化 (github.com)](https://github.com/fvtt-cn/pf2e_compendium_chn)
- [fvtt-cn/swade_compendium_chn: SWADE 合集包汉化 (github.com)](https://github.com/fvtt-cn/swade_compendium_chn)
- [fvtt-cn/pf1e_compendium_chn: Pathfinder 1e 合集包汉化 (github.com)](https://github.com/fvtt-cn/pf1e_compendium_chn)

## 参考
- [Simone Ricciardi / FoundryVTT Babele · GitLab](https://gitlab.com/riccisi/foundryvtt-babele)
