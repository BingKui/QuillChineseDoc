# 配置项

Quill允许通过多种方式来定制它以适应你的需求。本节致力于调整现有的功能。请参阅[模块（Modules）]()部分添加新功能和[主题（Themes）]()添加主题。

## 容器

Quill需要在编辑器中追加一个容器。你可以传入css选择器或者DOM对象。

```
var editor = new Quill('.editor');  // 将是使用第一个匹配的元素
```

```
var container = document.getElementById('editor');
var editor = new Quill(container);
```

```
var container = $('.editor').get(0);
var editor = new Quill(container);
```

## 配置项

通过传入一个配置项对象来配置Quill。

```
var options = {
  debug: 'info',
  modules: {
    toolbar: '#toolbar'
  },
  placeholder: 'Compose an epic...',
  readOnly: true,
  theme: 'snow'
};
var editor = new Quill('#editor', options);
```

以下的配置参数会被识别：

**bounds**

* Default：`document.body`

DOM元素或者一个DOM元素的css选择器，其中编辑器的UI元素（例如：tooltips）应该被包含其中。目前，只考虑左右边界。

**debug**

* Default：`warn`

debug的开关。注意：`debug`是一个静态方法并且会影响同一个页面的其它编辑器实例。只用警告和错误信息是默认启用的。

**formats**

* Default：All formats

在编辑器中允许的格式白名单。请参阅[格式化]()以获取完整列表。

**modules**

包含的模块和相应的选项。请参阅[模块]()以获取更多信息。

**placeholder**

* Default：none

编辑器为空时显示的占位符。

**readOnly**

* Default：`false`

是否将编辑器是实例设置为只读模式。

**scrollingContainer**

* Default：`null`

DOM元素或者一个DOM元素的css选择器，指定改容器具有滚动条（例如：`overflow-y: auto`），如果已经被用户自定义了默认的`ql-editor`。当Quill设置为自动适应高度是，需要修复滚动跳转的错误，并且另一个父容器负责滚动。

> 注意：当使用body时，一些浏览器仍然会跳转。可以使用一个单独的div子节点来避免这种情况。

**strict**

* Default：`true`

根据对semver的严格解释，一些改进和修改将保证主要版本的碰撞。为了防止扩大版本号的微小变化，它们被这个严格的标志禁止。具体的变化可以在Changelog中找到并搜索“strict”。将其设置为false可能会影响将来的改进。

**theme**

使用的主题名称。内置的选项有“bubble”和“snow”。无效或者假的值将加载默认的最小主题。注意：主题的特定样式仍然需要手动包含。请参阅[主题]()了解更多信息。









