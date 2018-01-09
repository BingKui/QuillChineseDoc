## 快速开始

最好的开发方法就是尝试一个简单的例子。Quill使用DOM元素初始化一个编辑器。这个元素的内容将成为Quill的初始化内容。

```
<!-- 引入样式文件 -->
<link href="https://cdn.quilljs.com/1.3.4/quill.snow.css" rel="stylesheet">

<!-- 创建一个编辑器容器 -->
<div id="editor">
  <p>Hello World!</p>
  <p>Some initial <strong>bold</strong> text</p>
  <p><br></p>
</div>

<!-- 引入Quill库文件 -->
<script src="https://cdn.quilljs.com/1.3.4/quill.js"></script>

<!-- 初始化Quill编辑器 -->
<script>
  var quill = new Quill('#editor', {
    theme: 'snow'
  });
</script>
```

这个就是一个简单例子的全部内容。

## 进一步了解

Quill的真正魔力来自于它的灵活性和可扩展性。你能够查看网站上所有的演示或者直接进入[Interactive Playground](https://quilljs.com/playground/)来了解更多。要深入了解，请查看[如何自定义Quill](https://quilljs.com/guides/how-to-customize-quill/)。