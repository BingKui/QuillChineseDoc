# Content

## deleteText

从编辑器删除文本，返回一个改变的Delta对象。操作来源可能是：‘user’、‘api’或者‘silent’。当编辑器被禁用时，来源‘user’将被忽略。

**Methods**

```
deleteText(index: Number, length: Number, source: String = 'api'): Delta
```

**Examples**

```
quill.deleteText(6, 4);
```

## getContents

检索编辑器的内容，格式化返回一个Delta对象。

**Methods**

```
getContents(index: Number = 0, length: Number = remaining): Delta
```

**Examples**

```
var delta = quill.getContents();
```

## getLength

检索返回编辑器的内容长度。注意：即使Quill是空的，编辑器仍然有一个‘\n’表示的空行，所以getLength将返回1。

**Methods**

```
getLength(): Number
```

**Examples**

```
var length = quill.getLength();
```

## getText

检索并已字符串的方式返回编辑器的内容。非空字符串会被忽略，因此返回的字符串长度可能比getLength返回的编辑器长度短。注意：即使Quill为空，依然存在一个空行，所以在这种情况下getText会返回一个‘\n’。

**Methods**

```
// index：开始位置索引  length：结束索引，默认为当前剩余文档的长度
getText(index: Number = 0, length: Number = remaining): String
```

**Examples**

```
var text = quill.getText(0, 10);
```

## insertEmbed

向编辑器中插入嵌入式内容，返回一个改变后的Delta对象。操作来源可能为：‘user’、‘api’或者‘silent’。当编辑器被禁用时，来源‘user’将被忽略。

**Methods**

```
insertEmbed(index: Number, type: String, value: any, source: String = 'api'): Delta
```

**Examples**

```
quill.insertEmbed(10, 'image', 'http://quilljs.com/images/cloud.png');
```

## insertText

向编辑器中插入文本，可以使用指定的格式或者多种格式。返回一个改变后的Delta对象。操作来源可能为：‘user’、‘api’或者‘silent’。当编辑器被禁用时，来源‘user’将被忽略。

**Methods**

```
insertText(index: Number, text: String, source: String = 'api'): Delta
insertText(index: Number, text: String, format: String, value: any,
           source: String = 'api'): Delta
insertText(index: Number, text: String, formats: { [String]: any },
           source: String = 'api'): Delta
```

**Examples**

```
quill.insertText(0, 'Hello', 'bold', true);

quill.insertText(5, 'Quill', {
  'color': '#ffff00',
  'italic': true
});
```

## pasteHTML

弃用

这个API已经被移动到Clipboard并且改名。它会在2.0的顶级API中删除。

## setContents

用给定的内容覆盖编辑器的内容。内容应该以一个新行或者换行符结束。返回一个改变的Delta。如果被给定的Delta没有无效操作，那么就会作为新的Delta通过。操作来源可能为：‘user’、‘api’或者‘silent’。当编辑器被禁用时，来源‘user’将被忽略。

**Methods**

```
setContents(delta: Delta, source: String = 'api'): Delta
```

**Examples**

```
quill.setContents([
  { insert: 'Hello ' },
  { insert: 'World!', attributes: { bold: true } },
  { insert: '\n' }
]);
```

## setText

使用给定的文本设置为编辑器的内容，返回一个改变后的Delta对象。注意：Quill文档必须以一个换行符结束，如果省略将会为你加一个。操作来源可能为：‘user’、‘api’或者‘silent’。当编辑器被禁用时，来源‘user’将被忽略。

**Methods**

```
setText(text: String, source: String = 'api'): Delta
```

**Examples**

```
quill.setText('Hello\n');
```

## updateContents

将Delta应用于编辑器的内容，返回一个改变后的Delta对象。如果这个Delta通过没有无效的操作，那么这些Deltas将是相同的。操作来源可能为：‘user’、‘api’或者‘silent’。当编辑器被禁用时，来源‘user’将被忽略。

**Methods**

```
updateContents(delta: Delta, source: String = 'api'): Delta
```

**Examples**

```
// 假设编辑器当前包含 [{ insert: 'Hello World!' }]
quill.updateContents(new Delta()
  .retain(6)                  // Keep 'Hello '
  .delete(5)                  // 'World' is deleted
  .insert('Quill')
  .retain(1, { bold: true })  // 将粗体应用于感叹号
});
// 编辑器现在应该是 [
//  { insert: 'Hello Quill' },
//  { insert: '!', attributes: { bold: true} }
// ]
```



