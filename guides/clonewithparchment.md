# 通过Parchment重写元素

为了提供一致的编辑体验，你需要一致的数据和可预测的行为。不幸的是DOM缺乏这两个特性。现代编辑器的解决方案是维护自己的文档类型来表示内容。Parchment就是Quill的解决方案。它是通过自己的额私有API来组织自己的代码库的。通过Parchment你可以自定义出Quill能够识别的内容和格式，或者添加全新的内容和格式。

在本指南中，我们将使用Parchment和Quill提供的模块在Medium上复制编辑器。我们将从基本的Quill开始，没有任何主题、模块和格式。在这个基础上，Quill只能够理解纯文本。在本指南结束时，链接、视频甚至推特都将被理解。

## 地基（Groundwork）

让我们开始，不使用Quill，仅仅使用文本域和按钮来连接到一个虚拟的事件监听器上。在本教程上，我们将使用jQuery方便操作，但是Quill和Parchment都不依赖于这个。我们也会在Google Fonts和Font Awesome的集成下添加一些基础的样式。这些与Quill和Parchment都没有任何关系，所以我们能够随时移除他们。

## 添加Quill核心库

下一步，我们会在没有任何主题、格式和多余模块的情况下把文本域替换成Quill核心库。打开开发控制台，检查输入。你可以在控制台看到一个Parchment的基本组成部分。

像DOM一样，Parchment文档也是一棵树。它的节点叫做Blots，是一个DOM节点的抽象。一些基础的Blots已经被定义，如：Scroll、Block、Inline、Text和Break。当你输入的时候，一个Text Blot与相应的DOM节点会进行同步；回车键会创建一个新的Block Blot。在Parchment中，Blots必须存在至少一个孩子节点，因此空的Block会填充一个Break Blot。这使得处理子节点是简单的和可预测的。所有的这些都在Scroll Blot下被组织。

你不能通过在一行上输入来观察Inline Blot，因为它不会为文档提供有意义的结构和格式。一个有效的Quill文档必须是规范和紧凑的。只用一个有效的DOM树能够表示给定搞得文档，并且该DOM树包含最少的节点数。

由于`<p><span>Text</span></p>`和`<p>Text</p>`表示相同的内容，因此前边的是无效的，并且去除`<span>`是Quill优化的一部分。同样一旦我们添加了样式，`<p><em>Te</em><em>st</em></p>`和`<p><em><em>Test</em></em></p>`也是无效的，应为他们不是最紧凑的。

由于这些限制，Quill不能支持任意的DOM树和HTML更改。但是正如我们所看到的，这种结构提供了一致性和可预测性，能够使我们轻松的创建丰富的编辑体验。

## 默认格式

我们前面提到过，Inline不能提供格式。这是基础内联类的例外，而不是规则。基础的Block Blot和它的工作方式相同。

为了实现粗体和斜体，我们只需要从Inline继承，然后设置`blotName`和`tagName`，并注册到Quill上就可以了。有关继承和静态方法和变量的签名的参考，请查阅[Parchment](../other/parchment.md)

```
let Inline = Quill.import('blots/inline');

class BoldBlot extends Inline { }
BoldBlot.blotName = 'bold';
BoldBlot.tagName = 'strong';

class ItalicBlot extends Inline { }
ItalicBlot.blotName = 'italic';
ItalicBlot.tagName = 'em';

Quill.register(BoldBlot);
Quill.register(ItalicBlot);
```

我们遵循Medium的例子，在这里使用了`strong`和`em`便签，但是你也可以用`b`和`i`标签。Blot的名称会被用作Quill格式的名称。通过注册我们的Blot，现在我们可以在我们的新格式上使用Quill完整的API。

```
Quill.register(BoldBlot);
Quill.register(ItalicBlot);

var quill = new Quill('#editor');

quill.insertText(0, 'Test', { bold: true });
quill.formatText(0, 4, 'italic', true);
// 如果我们把自己的Blot叫做 "myitalic"，我们应该这样写
// quill.formatText(0, 4, 'myitalic', true);
```

现在，让我们扔掉我们的虚拟按钮处理程序，并将粗体和斜体连接到Quill的`format()`。为了简单起见，我们将硬编码设置为true，始终添加格式。在你的程序中，你可以通过`getFormat()`来检索当前在任意范围内的格式，以决定是添加格式还是删除格式。工具栏（Toolbar）模块已经为Quill实现了这一点，我们不会再这里重新实现。

打开你的控制台，并使用心得粗体和斜体格式尝试Quill的API！

请注意，如果将粗体和斜体同时应用于某些文本，则无论按照何种顺序进行，Quill都会按照一致的顺序将`<strong>`标记封装在`<em>`标记之外。

## Links

链接稍微复杂一些，因为我们需要更多的布尔值来存储链接的URL。这会以两种方式影响我们的链接Blot：创建和格式检索。我们将url表示成为一个字符串值，但是我们可以通过其他方式轻松实现，例如一个带有url属性的对象，允许设置其他key/value对并定义一个链接。我们稍后将用images来演示这一点。

```
class LinkBlot extends Inline {
  static create(value) {
    let node = super.create();
    // 如果需要，可以清楚url的值
    node.setAttribute('href', value);
    // 好的，设置其他非格式相关的属性
    // 这些Parchment是不可见的，所以必须是静态的（static）
    node.setAttribute('target', '_blank');
    return node;
  }

  static formats(node) {
    // 我们只会在一个节点完成的时候调用
    // 确定是一个链接Blot，因此不需要检查己
    // not need to check ourselves
    return node.getAttribute('href');
  }
}
LinkBlot.blotName = 'link';
LinkBlot.tagName = 'a';

Quill.register(LinkBlot);
```

现在我们给我们的链接按钮绑定一个`prompt`，再次保持简单，然后传递给Quill的`format()`。

## 段落和标题

段落的执行方式与粗体相同，只不过我们将从块继承基本块级别Blot。虽然Inline Blots能够嵌套，但是Block Blots不能。当应用相同的文本范围时，Block Blots不会被另一个替代。

```
let Block = Quill.import('blots/block');

class BlockquoteBlot extends Block { }
BlockquoteBlot.blotName = 'blockquote';
BlockquoteBlot.tagName = 'blockquote';
```

标题的实现方式完全一样，只有一个区别：它能够由多个DOM元素表示。格式的默认值变成tagName而不是true。我们可以通过`formats()`来进行定制，就像我们为links做的一样。

```
class HeaderBlot extends Block {
  static formats(node) {
    return HeaderBlot.tagName.indexOf(node.tagName) + 1;
  }
}
HeaderBlot.blotName = 'header';
// Medium只支持两种的标题大小，多以我们呢智能演示两种
// 但是我们仍然能够给这个数组添加更多的tag
HeaderBlot.tagName = ['H1', 'H2'];
```

让我们把这些新的标签绑定到相应的按钮，并为`<blockquote>`标签添加一些css样式。

尝试在你的控制台通过运行`quill.getContents()`来设置一些文本到H1。你会看到我们自定义的静态`formats()`函数在工作。

## 分割线

现在，让我们来实现我们的第一个分页Blot。虽然我们以前的Blot实例能够提供格式化并且实现`format()`，但是分页Blot贡献内容并且实现`value()`。分页Blot可以是文本或者嵌入式Blot，所以我们的章节分割将是一个嵌入。一旦被被创建，嵌入式Blot的值是不能够改变的，需要删除和重新插入来更改该位置的内容。

我们的方法与之前的类似，只不过我们从BlockEmbed继承而来。Embed也存在于`blots/embed`下，但是它是内联级别的Blots。我们希望通过块级别实现代替分隔符。

```
let BlockEmbed = Quill.import('blots/block/embed');

class DividerBlot extends BlockEmbed { }
DividerBlot.blotName = 'divider';
DividerBlot.tagName = 'hr';
```

我们的点击事件叫做`insertEmbed()`，这个不像`format()`那样明确的指明确定、保存和恢复用户的选择，因此我们必须做更多的工作来保证自己的选择。除此之外，当我们尝试在中间的块中插入一个BlockEmbed时，Quill会为我们分隔块。为了使这个行为更加清楚，我们将在插入分隔符之前插入换行符来明确地分隔块。查看效果，了解具体细节。

## 图片



