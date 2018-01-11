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






