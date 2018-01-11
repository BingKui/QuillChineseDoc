# Events

## text-change

Quill内容改变时触发。提供了具体的变化细节，以及更改前的内容，还有更改的来源。如果更改来自用户那么来源为：‘user’。例如：

* 用户向编辑器中输入

* 用户通过工具栏格式化文本

* 用户通过快捷键执行撤销操作

* 用户使用操作系统拼写更正

通过API也可能会发生更改，但是只要他们来自用户，提供的来源仍然是‘user’。例如：当用户点击了工具栏，工具栏模块在技术上是调用Quill API来实现更改。但是来源仍然是‘user’，因为更改的根源是用户的点击。

导致文本变化的API的来源可能为`silent`，这种情况下`text-change`将不会被调用执行。这个操作是不被推荐的，应为它可能会打破撤销堆栈和其他依赖于文本更改的完整记录功能。

对文本的更改可能导致对其他选项的更改（如：光标移动），然而在`text-change`处理期间，其他选项尚未被更新，并且本地浏览器行为可能会使其处于不一致的状态。使用`selection-change`或者`editor-change`来获取可靠的选择更新。

**回调函数**

```
handler(delta: Delta, oldContents: Delta, source: String)
```

**例子**

```
quill.on('text-change', function(delta, oldDelta, source) {
  if (source == 'api') {
    console.log("An API call triggered this change.");
  } else if (source == 'user') {
    console.log("A user action triggered this change.");
  }
});
```

## selection-change

当用户或者API导致选择改变时调用，选择的范围作为界限。一个空范围表示选择丢失（通常由编辑器失去焦点引起）。你也可以通过检查调用事件的范围是否为空用作焦点更改事件。

导致选择更改的API也可以是`slient`，这种情况下`selection-change`将不会被调用执行。如果`selection-change`存在副作用，这个是很有用的。例如：输入能够导致选择改变，但是如果每个字符都调用`selection-change`的话会变得非常混乱。

**回调函数**

```
handler(range: { index: Number, length: Number },
        oldRange: { index: Number, length: Number },
        source: String)
```

**例子**

```
quill.on('selection-change', function(range, oldRange, source) {
  if (range) {
    if (range.length == 0) {
      console.log('User cursor is on', range.index);
    } else {
      var text = quill.getText(range.index, range.length);
      console.log('User has highlighted', text);
    }
  } else {
    console.log('Cursor not in the editor');
  }
});
```

## editor-change

当`text-change`或者`selection-change`执行后会被调用执行，甚至来源是`silent`。第一个参数是事件名称，是`text-change`或者`selection-change`，随后的通常是传入处理函数的参数。

**回调函数**

```
handler(name: String, ...args)
```

**例子**

```
quill.on('editor-change', function(eventName, ...args) {
  if (eventName === 'text-change') {
    // args[0] 将是Delta
  } else if (eventName === 'selection-change') {
    // args[0] 将是就得范围
  }
});
```

## on

添加事件监听。有关事件本身的更多信息，请参阅[text-change]()或者[selection-change]()。

**方法**

```
on(name: String, handler: Function): Quill
```

**例子**

```
quill.on('text-change', function() {
  console.log('Text change!');
});
```

## off

删除事件监听。

**方法**

```
off(name: String, handler: Function): Quill
```

**例子**

```
function handler() {
  console.log('Hello!');
}

quill.on('text-change', handler);
quill.off('text-change', handler);
```

## once

为一个方法添加第一次执行的事件。有关事件本身的更多信息，请参阅[text-change]()或者[selection-change]()。

**方法**

```
once(name: String, handler: Function): Quill
```

**例子**

```
quill.once('text-change', function() {
  console.log('First text change!');
});
```


