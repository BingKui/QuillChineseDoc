# 如何自定义Quill

Quill是在定制和扩展的思想下被设计的。这个是通过一个有细小颗粒度和良好API的微小编辑器核心实现的。核心模块是一个增强的模块，使用的是和你相同的API。

一般来说，用户可以通过配置项来进行定制，通过主题和CSS来定制化用户界面，通过模块定制功能，以及通过Parchment编辑内容。

## 配置

Quill偏向于编码配置，但是对于常见的需求，特别是在等效代码冗长或复杂的情况下，Quill提供了配置选项。

`modules`和`theme`是两个最主要的选项。你可以通过简单的添加或者删除个别模块或者使用不同主题，扩展Quill的功能或者改变Quill样式。

## 主题

Quill官方提供了一个标准的工具栏主题Snow和一个提示工具主题Bubble。由于Quill不同于其他传统浏览器那样被限制在iframe中，因此只需要使用现有的一个主题就可以在其基础上通过css进行许多可视化的修改。

如果你想大量的修改用户界面，你可以通过忽略theme的配置项来获得一个完全无样式的Quill编辑器。在这种情况下，你仍然需要一个包含基础样式的最小样式文件，来确保所有浏览器的对于空格有统一的呈现以及有序列表存在适当的编号。

```
<link rel="stylesheet" href="https://cdn.quilljs.com/1.3.4/quill.core.css">
```

现在，你可以实现和使用自己的用户界面元素，例如：自定义下拉菜单、提示工具。最后一章[Cloning Medium with Parchment]()提供了一个例子。

## 模块

Quill采用小型编辑器核心组成的模块化架构设计，其中包含增强其功能的模块。这些功能中的一部分对于编辑器来说非常重要美丽如：管理撤销和重做的历史记录模块。由于所有模块都使用了和暴露给开发人员相同的公共API，所以在必要时甚至可以更换核心模块。

像Quill的核心模块一样，许多模块都暴露了额外的配置项和APIs。在决定替换模块之前，请先看一看它的文档。通常你所需要的定制化需求已经作为配置项或者提供API来实现了。

除此之外，如果你想大量的改变现有模块已经实现的功能，你可以包含它，或者当一个主题默认包含它时明确的排除它，然后使用和默认模块相同的API来实现你自己的额外功能。

```
var quill = new Quill('#editor', {
  modules: {
    toolbar: false    // Snow includes toolbar by default
  },
  theme: 'snow'
});
```

一些模块，例如：[Clipboard]()、[Keyboard]()和[History]()，需要被作为核心功能包含，这取决于他们提供的APIs。例如，即使撤销和重做是基本、预期的编辑器功能，但是浏览器的默认行为是不符并且不可预期的。History模块通过自己实现的撤销管理器并将`undo()`和`redo()`暴露为API来填补这个空白。

尽管如此，坚持使用Quill模块化设计，仍然可以通过实现自己的撤销管理器来取代历史模块，从而彻底改变撤销和重做的方式，或者其他核心功能。只要你实现相同的API接口，Quill会很乐意使用你替换的模块。通过继承现有的模块，并覆盖你想要改变的地方，这就很容易完成。看看模块文档中的覆盖核心[剪贴板模块]()的一个非常简单的例子。

最终，你可能需要添加现有模块未能提供的功能。在这种情况下，将其组织成为一个Quill模块可能会很方便，[自定义模块]()包含了这些内容。当然，在应用程序代码中将逻辑从Quill中分离出来肯定是有效的。

## 内容和格式

Quill允许通过其文档模型Parchment修改和扩展它能够了解的内容和格式。内容和格式在Parchment中表示为Blots或者Attributors，大致对应DOM中的节点和属性。

### 类名（Class） 与 行样式（Inline）的比较

在尽可能的情况下，Quill使用类而不是使用内联样式属性，但两者都可以实现，你可自行选择。下面是一个实例的片段。

```
var ColorClass = Quill.import('attributors/class/color');
var SizeStyle = Quill.import('attributors/style/size');
Quill.register(ColorClass, true);
Quill.register(SizeStyle, true);

// 像普通方式一样初始化
var quill = new Quill('#editor', {
  modules: {
    toolbar: true
  },
  theme: 'snow'
});
```

### 自定义属相（Customizing Attributors）

除了选择特定的属性外，你也可以定制现有的。以下是字体白名单添加附加字体的示例。

```
var FontAttributor = Quill.import('attributors/class/font');
FontAttributor.whitelist = [
  'sofia', 'slabo', 'roboto', 'inconsolata', 'ubuntu'
];
Quill.register(FontAttributor, true);
```

注意，你仍然需要将这些类的样式添加到css文件中。

```
<link href="https://fonts.googleapis.com/css?family=Roboto" rel="stylesheet">
<style>
.ql-font-roboto {
  font-family: 'Roboto', sans-serif;
}
</style>
```

### 自定义Blots

由Blots代表的格式也可以定制。以下是如何更改用于表示粗体格式的DOM节点。

```
var Bold = Quill.import('formats/bold');
Bold.tagName = 'B';   // Quill uses <strong> by default
Quill.register(Bold, true);

// 正常初始化
var quill = new Quill('#editor', {
  modules: {
    toolbar: true
  },
  theme: 'snow'
});
```

### 扩展Blots

你也可以扩展现有的格式。下边是一个不允许格式化其内容的列表项的ES6实现。代码块正是以这种方式实现的。

```
var ListItem = Quill.import('formats/list/item');

class PlainListItem extends ListItem {
  formatAt(index, length, name, value) {
    if (name === 'list') {
      // Allow changing or removing list format
      super.formatAt(name, value);
    }
    // Otherwise ignore
  }
}

Quill.register(PlainListItem, true);

// 正常初始化
var quill = new Quill('#editor', {
  modules: {
    toolbar: true
  },
  theme: 'snow'
});
```

你可以通过调用`console.log(Quill.imports)`查询可用的Blots和Attributors列表。不支持直接修改这个对象。使用`Quill.register`代替。

有关Parchment、Blots和Attributors的完整参考资料可以在Parchment自己的文档中找到。要深入的了解，请查阅[通过Parchment克隆Medium]()，该文档从Quill了解纯文本开始，添加所有格式的支持。大多数情况下，由于绝大多数已经在Quill中实现，所以不需要从头开始构建格式，但是了解Quill的工作原理仍然很有用。
