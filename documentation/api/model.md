# Model

> 说明：实验性api对于语义化版本不适用

## find （实验性api）

静态方法，返回给定的DOM节点的Quill或者Blot实例。后面一种情况下，如果传入的参数为`bubble`，则会向上查询DOM的父节点，直到找到相应的Blot。

**方法**

```
Quill.find(domNode: Node, bubble: boolean = false): Blot | Quill
```

**例子**

```
var container = document.querySelector("#container");
var quill = new Quill(container);
console.log(Quill.find(container) === quill);   // Should be true

quill.insertText(0, 'Hello', 'link', 'https://world.com');
var linkNode = document.querySelector('#container a');
var linkBlot = Quill.find(linkNode);
```

## getIndex （实验性api）

返回文档开始于给定的Blot之间的距离。

**方法**

```
getIndex(blot: Blot): Number
```

**例子**

```
let [line, offset] = quill.getLine(10);
let index = quill.getIndex(line);   // index + offset should == 10
```

## getLeaf

返回文档给定索引出的叶子Blot。

**方法**

```
getLeaf(index: Number): Blot
```

**例子**

```
quill.setText('Hello Good World!');
quill.formatText(6, 4, "bold", true);

let [leaf, offset] = quill.getLeaf(7);
// leaf应该是一个值为“Good”的Text Blot
// 偏移值应该是1，因为开始的索引为6
```

## getLine （实验性api）

返回文档给定索引出的行级Blot。

**方法**

```
getLine(index: Number): [Blot, Number]
```

**例子**

```
quill.setText('Hello\nWorld!');

let [line, offset] = quill.getLine(7);
// line应该是一个第二行“World！”的Block Blot
// 偏移值应该是1，因为开始的索引为6
```

## getLines （实验性api）

返回指定位置内的所有行。

**方法**

```
getLines(index: Number = 0, length: Number = remaining): Blot[]
getLines(range: Range): Blot[]
```

**例子**

```
quill.setText('Hello\nGood\nWorld!');
quill.formatLine(1, 1, 'list', 'bullet');

let lines = quill.getLines(2, 5);
// 一个ListItem的数组和Block Blot,
// 代表前两行
```






