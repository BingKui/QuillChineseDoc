# Parchment

Parchment是Quill的文档模型。它是一个并行的树结构，并且提供对内容的编辑（如Quill）的功能。一个Parchment树是有Blots组成的，它反映了一个DOM对应的节点。Blots能够提供结构、格式和内容或者只有内容。Attributors能够提供轻量级的格式化信息。

> 注意：你不应该使用`new`来实例化一个Blot。这个方法可能阻止Blot的必要生命周期。使用带有注册功能的`create()`方法代替。

```
npm install --save parchment
```

可以查看[Parchment使用简介]()来了解Quill是如何使用Parchment的文档模型的。

## Blots

Blots是Parchment文档的基本组成部分。提供了几个基本的实现，如：Block、Inline和Embed。一般来说，你会想扩展其中的一个，而不是从头开始构建。实现之后，需要在使用之前进行注册。

一个最基本的Blots必须使用一个静态的blotName来命名，并且有一个与之相关联的tagName或者className。如果一个Blot是通过标签和类定义的，类是第一优先级，标签被用作备用。Blots还必须有一个范围，来确定他是内联（inline）还是分块（block）。

```
class Blot {
  static blotName: string;
  static className: string;
  static tagName: string;
  static scope: Scope;

  domNode: Node;
  prev: Blot;
  next: Blot;
  parent: Blot;

  // 创建相应的节点
  static create(value?: any): Node;

  constructor(domNode: Node, value?: any);

  // 对于子集，是Blot的长度
  // 对于父节点，是所有子节点的总和
  length(): Number;

  // 如果适用，按照给定的指数和长度进行操作
  // 经常会把响应转移到合适的子节点上
  deleteAt(index: number, length: number);
  formatAt(index: number, length: number, format: string, value: any);
  insertAt(index: number, text: string);
  insertAt(index: number, embed: string, value: any);

  // 返回当前Blot与父节点之间的偏移量
  offset(ancestor: Blot = this.parent): number;

  // 更新的生命周期之后调用。
  // 不能修改文档的值和长度，并且任何DOM操作都必须降低DOM树的复杂性。
  // 共享上下文对象被传递给所有的Blots。
  optimize(context: {[key: string]: any}): void;

  // 当Blot改变是调用，伴随着改变记录。
  // blot的内部值能够被更新，并且允许修改Blot本身。
  // 可以通过用户行为或者API调用触发。
  // 共享上下文对象被传递给所有的Blots。
  update(mutations: MutationRecord[], context: {[key: string]: any});


  /** 仅对于作为子节点 **/

  // 如果是Blot的类型，返回由domNode表示的值。
  // 本身没有对domNode的类型进行校验，需要应用程序在调用之前进行外部校验。
  static value(domNode): any;

  // 给定一个node和一个在DOM选择范围内的偏移量，返回一个该位置的索引。
  index(node: Node, offset: number): number;

  // 给定一个Blot的位置信息坐标，返回当前节点在DOM可选范围的偏移量
  position(index: number, inclusive: boolean): [Node, number];

  // 返回当前Blot代表的值
  // 除了来自于API或者通过update检测的用户改变，不应该被改变。
  value(): any;


  /** 仅对于作为父节点 **/

  // Blots的白名单上数组，可以是直接的子节点
  static allowedChildren: Blot[];

  // 默认节点，当节点为空时会被插入
  static defaultChild: string;

  children: LinkedList<Blot>;

  // 在构造时调用，应该填写子节点的LinkedList
  build();

  // 有用的后代搜索功能，不应该被修改
  descendant(type: BlotClass, index: number, inclusive): Blot
  descendents(type: BlotClass, index: number, length: number): Blot[];


  /** 仅对于作为格式表 **/

  // 如果是Blot的类型，返回domNode的格式化后的值
  // 不需要检查domNode是否为Blot的类型
  static formats(domNode: Node);

  // Blot应用格式。不应该传递个子节点或者其他Blot
  format(format: name, value: any);

  // 返回格式代表的Blot，包括来自于Attributors的。
  formats(): Object;
}
```

## Example

表示链接的Blot实现，该链接是父级，内联范围和格式表。

```
import Parchment from 'parchment';

class LinkBlot extends Parchment.Inline {
  static create(url) {
    let node = super.create();
    node.setAttribute('href', url);
    node.setAttribute('target', '_blank');
    node.setAttribute('title', node.textContent);
    return node;
  }

  static formats(domNode) {
    return domNode.getAttribute('href') || true;
  }

  format(name, value) {
    if (name === 'link' && value) {
      this.domNode.setAttribute('href', value);
    } else {
      super.format(name, value);
    }
  }

  formats() {
    let formats = super.formats();
    formats['link'] = LinkBlot.formats(this.domNode);
    return formats;
  }
}
LinkBlot.blotName = 'link';
LinkBlot.tagName = 'A';

Parchment.register(LinkBlot);
```

Quill再其源码中提供了很多实现的示例。

## Block Blot

块类型格式化Blot的基本实现。默认格式的块级Blot会替代Blot的适当部分。

## Inline Blot

行级格式化Blot的基本实现。默认格式的行级Blot或者用一个Blot包裹自己，或者将它传递给合适的子节点。

## Embed Blot

非文本节点的基本实现，可以被格式化。其对应的额DOM节点通常是一个[Void元素]()，也可以是一个[正常元素]()。在这些情况下，Parchment将不会操作或者感知到元素的子元素，正确的执行Blot的`index()`和`position()`方法对于正确的光标显示/选区是很重要的。

## Scroll

Parchment文档的根节点。不能够被格式化。

## Attributors

Attributors是一种轻量级的格式话方式。它们的DOM对应的是[属性]()。像DOM属性和节点的关系一样，Attributors也属于Blots。调用Inline或者Block Blot的`formats()`方法将会返回相应的DOM节点的格式（如果有的话）以及DOM节点属性表示的格式（如果有的话）。

Attributors 有以下的以下接口：

```
class Attributor {
  attrName: string;
  keyName: string;
  scope: Scope;
  whitelist: string[];

  constructor(attrName: string, keyName: string, options: Object = {});
  add(node: HTMLElement, value: string): boolean;
  canAdd(node: HTMLElement, value: string): boolean;
  remove(node: HTMLElement);
  value(node: HTMLElement);
}
```

注意：自定义的属性是实例，而不是类似于Blots的类定义。类似于Blots，你可能希望使用现有的Attributors实现，而不是从头开始创建，比如基础的[Attritor]()、[Class Attributor]()或者[Style Attributor]()。

Attributors的实现非常简单，并且它的源代码可能是另一个库的资源。

### Attributor

使用一个普通属性来表示格式。

```
import Parchment from 'parchment';

let Width = new Parchment.Attributor.Attribute('width', 'width');
Parchment.register(Width);

let imageNode = document.createElement('img');

Width.add(imageNode, '10px');
console.log(imageNode.outerHTML);   // Will print <img width="10px">
Width.value(imageNode);                    // Will return 10px
Width.remove(imageNode);
console.log(imageNode.outerHTML);   // Will print <img>
```

### Class Attributor

使用类名模式来表示格式。

```
import Parchment from 'parchment';

let Align = new Parchment.Attributor.Class('align', 'blot-align');
Parchment.register(Align);

let node = document.createElement('div');
Align.add(node, 'right');
console.log(node.outerHTML);  // Will print <div class="blot-align-right"></div>
```

### Style Attributor

使用行样式来表示格式。

```
import Parchment from 'parchment';

let Align = new Parchment.Attributor.Style('align', 'text-align', {
  whitelist: ['right', 'center', 'justify']   // Having no value implies left align
});
Parchment.register(Align);

let node = document.createElement('div');
Align.add(node, 'right');
console.log(node.outerHTML);  // Will print <div style="text-align: right;"></div>
```

## Registry

除了`Parchment.create('bold')`的所有方法都可以从Parchment中获得。

```
// 通过名称或者DOM节点创建一个Blot
// 当只给定一个范围，创建一个范围相同名称的Blot
create(domNode: Node, value?: any): Blot;
create(blotName: string, value?: any): Blot;
create(scope: Scope): Blot;

// 通过一个DOM节点找到相应的Blot
// 当搜索一个嵌入式的Blot节点时，冒泡算法是很有用的。
// DOM几点的后代节点。
find(domNode: Node, bubble: boolean = false): Blot;

// 搜索Blot或者Attributor
// 当给定一个范围是，根据相应范围查找，返回相应的Blot
query(tagName: string, scope: Scope = Scope.ANY): BlotClass;
query(blotName: string, scope: Scope = Scope.ANY): BlotClass;
query(domNode: Node, scope: Scope = Scope.ANY): BlotClass;
query(scope: Scope): BlotClass;
query(attributorName: string, scope: Scope = Scope.ANY): Attributor;

// 注册Blot类定义或Attributor实例
register(BlotClass | Attributor);
```



