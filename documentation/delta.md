# Delta

Deltas是一种简单而富有表现力的格式，可以用来描述Quill的内容和改变。这种格式本质上是JSON，是人类可读的，也很容易被机器解析。Deltas可以描述任何Quill文档，包括所有的文本和格式信息，其中没有HTML的歧义和复杂性。

不要混淆它的名字Delta-Deltas代表文档和文档的变化。如果你Deltas是一个文档到另一个文档的说明，Deltas代表文档的方式是通过一个空文档开始说明的。

Deltas是作为一个独立库实现的，允许在Quill之外使用。它适用于操作转换，可以实时使用像Google Docs一样的应用程序。有关Deltas的更深入解释，请参阅[Delta格式的设计]()。

> 注意：不建议手动构建Deltas，可以使用链式方法`insert()`、`delete()`和`retain()`调用来创建新的Deltas。

## 文档

Delta的格式是显而易见的，例如下面的例子："Gandalf the Grey"的字符串其中"Gandalf"加粗，"Grey"的颜色是`#cccccc`。

```
{
  ops: [
    { insert: 'Gandalf', attributes: { bold: true } },
    { insert: ' the ' },
    { insert: 'Grey', attributes: { color: '#cccccc' } }
  ]
}
```

正如名字所表达的那样，描述内容对于Deltas来说实际上是一个特例。上面的例子很具体的说明了如何插入一个粗体字符‘Gandalf’，一个未格式化的字符串‘the’，和一个颜色为`#cccccc`的‘Gray’。当使用Deltas来描述文档时，如果将Delta应用于空白文档，则可以将其视为创建的内容。

由于Delta是一种数据格式，因此`attribute`对应的值，没有固定的意义。例如：Delta格式中没有规定颜色值必须在十六进制中，这个是Quill的选择，如果需要可以使用Parchment进行修改。

### Embeds

对于非文本内容（如图像或者公式），插入值可以是对象。该对象应该有一个唯一键，用来确定他的类型。如果你使用Parchment构建自定义内容，则为`blotName`。与文本类似，embeds仍然可以使用属性键来描述要应用于内容格式。所有的Embeds的长度都为1。

```
{
  ops: [{
    // An image link
    insert: {
      image: 'https://quilljs.com/assets/images/icon.png'
    },
    attributes: {
      link: 'https://quilljs.com'
    }
  }]
}
```

### 行级格式（Line Formatting）

与换行符相关联的属性，描述了该行的格式。

```
{
  ops: [
    { insert: 'The Two Towers' },
    { insert: '\n', attributes: { header: 1 } },
    { insert: 'Aragorn sped on up the hill.\n' }
  ]
}
```

所有的Quill文档都必须以换行符结尾，即使没有格式应用于最后一行。这样，你将始终有一个字符位置来应用行格式。

许多行格式是独占的。例如Quill不允许一行同时作为标题和列表，尽管可以使用Delta格式表示。

## 改变（changes）

在注册Quill的`text-chagne`事件时，你会获得一个描述Delta改变了什么的参数。除了`insert`操作，这个Delta可能有`delete`或者`retain`操作。

### Delete

删除操作明确的指出它意味着：删除下一个字符数。

```
{
  ops: [
    { delete: 10 }  // Delete the next 10 characters
  ]
}
```

由于删除操作不包含被删除的内容，因此Delta不可逆。

### Retain

保留操作仅仅意味着保留下一个字符数，而不需要修改。如果指定了`attributes`，它仍然意味着保留这些字符，但应用由属性对象指定的格式。如果属性值为`null`用户删除指定格式。

以一个‘Gandalf the Grey’的例子开始：

```
// {
//   ops: [
//     { insert: 'Gandalf', attributes: { bold: true } },
//     { insert: ' the ' },
//     { insert: 'Grey', attributes: { color: '#cccccc' } }
//   ]
// }

{
  ops: [
    // Unbold and italicize "Gandalf"
    { retain: 7, attributes: { bold: null, italic: true } },

    // Keep " the " as is
    { retain: 5 },

    // Insert "White" formatted with color #fff
    { insert: "White", attributes: { color: '#fff' } },

    // Delete "Grey"
    { delete: 4 }
  ]
}
```

请注意，Delta的说明始终始于文档的开头。由于保留操作的简单性，我们永远不需要为删除或者插入指定索引。
