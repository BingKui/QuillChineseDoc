# 工具栏模块

工具栏模块允许用户轻松的格式化Quill的内容。

它能够被配置一个自定义的容器和处理程序。

```
var quill = new Quill('#editor', {
  modules: {
    toolbar: {
      container: '#toolbar',  // Selector for toolbar container
      handlers: {
        'formula': customFormulaHandler
      }
    }
  }
});
```

因为容器选项非常普遍，所以也允许使用短写方法。

```
var quill = new Quill('#editor', {
  modules: {
    // Equivalent to { toolbar: { container: '#toolbar' }}
    toolbar: '#toolbar'
  }
});
```

## 容器

工具栏控件可以由一个简单的格式化名称是数组或者一个自定义的HTML容器来指定。

使用更简单的数组选项：

```
var toolbarOptions = ['bold', 'italic', 'underline', 'strike'];

var quill = new Quill('#editor', {
  modules: {
    toolbar: toolbarOptions
  }
});
```

控件也可以按照嵌套数组进行级别的分组。这将把`<span>`中的控件封装成类名为`ql-formats`的格式，为使用的主题提供结构。例如：Snow主题在控制组之间增加了额外的间距。

```
var toolbarOptions = [['bold', 'italic'], ['link', 'image']];
```

具有自定义值的按钮可以使用名称作为对象的唯一值。

```
var toolbarOptions = [{ 'header': '3' }];
```

下拉菜单同样使用一个对象配置，只不国是一个存在所有可能性值的数组。使用css来控制下拉菜单的标签显示。

```
// Note false, not 'normal', is the correct value
// quill.format('size', false) removes the format,
// allowing default styling to work
var toolbarOptions = [
  { size: [ 'small', false, 'large', 'huge' ]}
];
```

注意：主题还可以指定下拉菜单的默认值。例如Snow为字体颜色和背景颜色提供过了35中默认颜色，当设置为空时生效。

```
var toolbarOptions = [
  ['bold', 'italic', 'underline', 'strike'],        // 切换按钮
  ['blockquote', 'code-block'],

  [{ 'header': 1 }, { 'header': 2 }],               // 用户自定义按钮值
  [{ 'list': 'ordered'}, { 'list': 'bullet' }],
  [{ 'script': 'sub'}, { 'script': 'super' }],      // 上标/下标
  [{ 'indent': '-1'}, { 'indent': '+1' }],          // 减少缩进/缩进
  [{ 'direction': 'rtl' }],                         // 文本下划线

  [{ 'size': ['small', false, 'large', 'huge'] }],  // 用户自定义下拉
  [{ 'header': [1, 2, 3, 4, 5, 6, false] }],

  [{ 'color': [] }, { 'background': [] }],          // 主题默认下拉，使用主题提供的值
  [{ 'font': [] }],
  [{ 'align': [] }],

  ['clean']                                         // 清除格式
];

var quill = new Quill('#editor', {
  modules: {
    toolbar: toolbarOptions
  },
  theme: 'snow'
});
```

对于需要更多的定制需求，你可以手动在HTML中创建一个工具栏，并将DOM元素或者选择器传递给Quill。

`ql-toolbar`类将被添加到工具栏容器中，而Quill将相应的处理事件绑定到类名为`ql-{format}`格式的`<button>`和`<select>`元素上。按钮元素可以选择性添加自定义value值。

```
<!-- Create toolbar container -->
<div id="toolbar">
  <!-- Add font size dropdown -->
  <select class="ql-size">
    <option value="small"></option>
    <!-- Note a missing, thus falsy value, is used to reset to default -->
    <option selected></option>
    <option value="large"></option>
    <option value="huge"></option>
  </select>
  <!-- Add a bold button -->
  <button class="ql-bold"></button>
  <!-- Add subscript and superscript buttons -->
  <button class="ql-script" value="sub"></button>
  <button class="ql-script" value="super"></button>
</div>
<div id="editor"></div>

<!-- Initialize editor with toolbar -->
<script>
  var quill = new Quill('#editor', {
    modules: {
      toolbar: '#toolbar'
    }
  });
</script>
```

注意：通过自己提供HTML元素生成工具条，Quill会检索特定元素生成系统的工具栏绑定到Quill，但是你自己的自定义输入元素将不会绑定到Quill，但仍然能够被添加到工具条、可以设置样式。

```
<div id="toolbar">
  <!-- 像之前一样添加一个按钮 -->
  <button class="ql-bold"></button>
  <button class="ql-italic"></button>

  <!-- 但是，你仍然可以添加自己的 -->
  <button id="custom-button"></button>
</div>
<div id="editor"></div>

<script>
var quill = new Quill('#editor', {
  modules: {
    toolbar: '#toolbar'
  }
});

var customButton = document.querySelector('#custom-button');
customButton.addEventListener('click', function() {
  console.log('Clicked!');
});
</script>
```

## 事件

工具栏空间默认应用并删除样式，但是你也可以用自定义事件将其覆盖，例如：为了展示额外的用户界面。

事件函数将被绑定到工具栏上（因此使用`this`将得到工具栏的实例），如果相应的格式是非活动的，则传递输入的`value`属性，否则返回`false`。添加自定义事件将覆盖默认的工具栏和主题行为。

```
var toolbarOptions = {
  handlers: {
    // 事件对象将于默认的事件处理对象合并
    'link': function(value) {
      if (value) {
        var href = prompt('Enter the URL');
        this.quill.format('link', href);
      } else {
        this.quill.format('link', false);
      }
    }
  }
}

var quill = new Quill('#editor', {
  modules: {
    toolbar: toolbarOptions
  }
});

// 处理事件也可以在初始化之后进行添加
var toolbar = quill.getModule('toolbar');
toolbar.addHandler('image', showImageUI);
```
