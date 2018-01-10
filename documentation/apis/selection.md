# Selection

## getBounds

检索给定位置的像素位置（相对于编辑器容器）和选区的尺寸。用户的当前选择不需要在该索引处。用于计算工具提示的放置位置。

**Methods**

```
getBounds(index: Number, length: Number = 0):
  { left: Number, top: Number, height: Number, width: Number }
```

**Examples**

```
quill.setText('Hello\nWorld\n');
quill.getBounds(7);  // 返回值 { height: 15, width: 0, left: 27, top: 31 }
```

## getSelection

检索用户的选择范围，可选地首先关注编辑器。除此之外，如果编辑没有焦点，可能会返回一个`null`。

**Methods**

```
getSelection(focus = false): { index: Number, length: Number }
```

**Examples**

```
var range = quill.getSelection();
if (range) {
  if (range.length == 0) {
    console.log('User cursor is at index', range.index);
  } else {
    var text = quill.getText(range.index, range.length);
    console.log('User has highlighted: ', text);
  }
} else {
  console.log('User cursor is not in editor');
}
```

## setSelection

将用户的选择设置为给定的范围，这个主要用在编辑器上。提供`null`作为选择范围将模糊编辑器。来源可能是‘user’、‘api’或者‘silent’。

**Methods**

```
setSelection(index: Number, length: Number, source: String = 'api')
setSelection(range: { index: Number, length: Number },
             souce: String = 'api')
```

**Examples**

```
quill.setSelection(0, 5);
```



