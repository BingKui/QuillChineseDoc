# Formatting

## format

格式化用户当前选择的文本，返回一个改变后的Delta对象。如果用户选择的长度为0，即使是一个光标，格式将被设置为激活状态，因此，用户键入的下一个字符将具有当前的格式。操作来源可能为：‘user’、‘api’或者‘silent’。当编辑器被禁用时，来源‘user’将被忽略。

**Methods**

```
format(name: String, value: any, source: String = 'api'): Delta
```

**Examples**

```
quill.format('color', 'red');
quill.format('align', 'right');
```

## formatLine

格式化给定范围内的所有行，返回一个改变后的Delta对象。请参阅格式化列表以获取可用的格式。要删除格式，参数请传false。用户的选择可能不会被保留。操作来源可能为：‘user’、‘api’或者‘silent’。当编辑器被禁用时，来源‘user’将被忽略。

**Methods**

```
formatLine(index: Number, length: Number, source: String = 'api'): Delta
formatLine(index: Number, length: Number, format: String, value: any,
           source: String = 'api'): Delta
formatLine(index: Number, length: Number, formats: { [String]: any },
           source: String = 'api'): Delta
```

**Examples**

```
quill.setText('Hello\nWorld!\n');

quill.formatLine(1, 2, 'align', 'right');   // right aligns the first line
quill.formatLine(4, 4, 'align', 'center');  // center aligns both lines
```

## formatText

格式化编辑器中的文本，返回一个改变后的Delta对象。对于行级格式，例如文本对齐、定位换行符或者其他请使用`formatLine`帮助器。请参阅格式化列表以获取可用的格式。要删除格式，参数请传false。用户的选择可能不会被保留。操作来源可能为：‘user’、‘api’或者‘silent’。当编辑器被禁用时，来源‘user’将被忽略。

**Methods**

```
formatText(index: Number, length: Number, source: String = 'api'): Delta
formatText(index: Number, length: Number, format: String, value: any,
           source: String = 'api'): Delta
formatText(index: Number, length: Number, formats: { [String]: any },
           source: String = 'api'): Delta
```

**Examples**

```
quill.setText('Hello\nWorld!\n');

quill.formatText(0, 5, 'bold', true);      // 加粗 'hello'

quill.formatText(0, 5, {                   // 取消加粗 'hello' 并且设置颜色为blue
  'bold': false,
  'color': 'rgb(0, 0, 255)'
});

quill.formatText(5, 1, 'align', 'right');  //  'hello' 行右对齐
```

## getFormat

检索给定范围内文本的所用格式。对于返回的格式，范围内的所有文本存在一个拥有此格式。如果他们拥有不同的格式，那么将返回一个所有格式的数组。如果没有给定范围，那么将使用当前用户选择的范围。可以用来显示光标上已经设置的那些格式。如果无参调用，那么将使用当前用户选择的范围。

**Methods**

```
getFormat(range: Range = current): { [String]: any }
getFormat(index: Number, length: Number = 0): { [String]: any }
```

**Examples**

```
quill.setText('Hello World!');
quill.formatText(0, 2, 'bold', true);
quill.formatText(1, 2, 'italic', true);
quill.getFormat(0, 2);   // { bold: true }
quill.getFormat(1, 1);   // { bold: true, italic: true }

quill.formatText(0, 2, 'color', 'red');
quill.formatText(2, 1, 'color', 'blue');
quill.getFormat(0, 3);   // { color: ['red', 'blue'] }

quill.setSelection(3);
quill.getFormat();       // { italic: true, color: 'blue' }

quill.format('strike', true);
quill.getFormat();       // { italic: true, color: 'blue', strike: true }

quill.formatLine(0, 1, 'align', 'right');
quill.getFormat();       // { italic: true, color: 'blue', strike: true,
                         //   align: 'right' }
```

## removeFormat

删除给定范围内的所有格式和嵌入，返回一个改变后的Delta对象。如果当前行的任何部分包含在范围内，行格式将被删除。用户的选择可能不会被保留。操作来源可能为：‘user’、‘api’或者‘silent’。当编辑器被禁用时，来源‘user’将被忽略。

**Methods**

```
removeFormat(index: Number, length: Number, source: String = 'api'): Delta
```

**Examples**

```
quill.setContents([
  { insert: 'Hello', { bold: true } },
  { insert: '\n', { align: 'center' } },
  { insert: { formula: 'x^2' } },
  { insert: '\n', { align: 'center' } },
  { insert: 'World', { italic: true }},
  { insert: '\n', { align: 'center' } }
]);

quill.removeFormat(3, 7);
// 编辑器内容现在应该是
// [
//   { insert: 'Hel', { bold: true } },
//   { insert: 'lo\n\nWo' },
//   { insert: 'rld', { italic: true }},
//   { insert: '\n', { align: 'center' } }
// ]
```



