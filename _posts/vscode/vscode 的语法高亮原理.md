## 基本原理

语法高亮决定了源代码不同部分的颜色和风格。语法高亮由两部分组成：

+ Tokenization：将源代码分割为一系列的符号
+ Theming：使用主题或者用户设置来将不同类型的符号映射为不同的颜色和风格

### Tokenization

VSCode 的 Tokenization 采用了 [TextMate](https://macromates.com/manual/en/language_grammars) 语法。 TextMate 语法文件可以是一个 plist(XML) 或者 JSON 文件。文件的内容是一系列正则表达式，每一个正则表达式有一个类型。 VSCode 会使用该语法文件中的正则表达式去匹配源代码文件的每一行中的每一个短语（Word），匹配上的短语就被赋予相应的类型。

下面是一个很简单的 textmate 语法文件样例：

```textmate
 1  {  scopeName = 'source.untitled';
 2     fileTypes = ( );
 3     foldingStartMarker = '\{\s*$';
 4     foldingStopMarker = '^\s*\}';
 5     patterns = (
 6        {  name = 'keyword.control.untitled';
 7           match = '\b(if|while|for|return)\b';
 8        },
 9        {  name = 'string.quoted.double.untitled';
10           begin = '"';
11           end = '"';
12           patterns = ( 
13              {  name = 'constant.character.escape.untitled';
14                 match = '\\.';
15              }
16           );
17        },
18     );
19  }
```

`if`, `while`, `for`, `return` 在匹配时就会被赋予类型 `keyword.control.untitled`。 vscode 的语法高亮文件都位于源代码 extensions 目录下各种语言插件中，名字叫做：xx.tmLanguage.json, 例如 c 语言的语法高亮文件为：`c.tmLanguage.json`。

### Theming

主题将不同的颜色和风格赋予不同类型的符号。 主题配置文件通常也是一个 json 文件，比如 vscode 的 `dark (Visual Studio)` 主题的配置文件为：`dark_vs.json`， 位于`extensions/theme-defaults/themes` 目录下。 我们摘录其中的一小部分：

```json
{
	"scope": "keyword.control",
	"settings": {
		"foreground": "#569cd6"
	}
},
```

这段的意思就是说对于类型为 `keyword.control` 的符号，我们将其前景色设置为 `#569cd6`。

VSCode 通过分词（Tokenization）得到了每一个词语的类型，然后从主题文件中找到该类型的词语应该被赋予什么颜色。这就是语法高亮最基本的原理。了解了语法高亮的基本原理，那么下面我们将从以下几个方面来看 VSCode 是如何在源码中实现这些步骤的。

1. TextMate 语法文件 xx.tmLanguage.json 何时被加载？
2. 分词是怎么进行的？
3. 主题文件 `dark_vs.json` 是何时读取的？
4. 词语的类型与主题中的 scope 是怎么匹配的？
5. 颜色是怎么设置，怎么渲染出来的？

## TextMate 语法文件的读取

我们以c语言语法高亮文件`extensions/cpp/syntaxes/c.tmLanguage.json`为例来分析。TextMate 语法文件的读取由 npm库 `vscode-textmate` 实现。该库支持加载 JSON 或者 PLIST 格式的 textmate 语法文件。



## 分词是怎么进行的？

vscode 的分词是通过调用

分词后所有的 token 都存储在一个 `ContiguousTokensStore` 中，该类位于文件 `vs/editor/common/tokens/contiguousTokensStore.ts` 文件中。