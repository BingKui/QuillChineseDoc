# 历史模块

历史模块主要负责Quill的撤销和重做。可以使用以下选项进行配置：

## 配置项

### delay

* Default：`1000`

在延迟多少毫秒数内发生的更改合并为单个更改。

例如，如果设置延迟为0，几乎每个字符都会被记录成一个变化，所以撤销会一次撤销一个字符。如果设置延迟为1000，撤销会撤销在最近1000毫秒内发生的所有更改。

### maxStack

* Default：`100`

历史撤销/重做堆栈的最大大小。在延时内的合并更改，计为单一更改。

### userOnly

* Default：false

默认情况下，无论是用户的操作还是通过调用API的操作都会被看做是相同的更改，并且由历史模块改变撤消或重做。如果userOnly设置为true，则只用用户的更改会被撤销或者重做。

**例子**

```
var quill = new Quill('#editor', {
  modules: {
    history: {
      delay: 2000,
      maxStack: 500,
      userOnly: true
    }
  },
  theme: 'snow'
});
```

## API

### clear

清理历史堆栈。

**方法**

```
clear()
```

**例子**

```
quill.history.clear();
```

### cutoff (实验性api)

正常情况下，连续进行的更改（在配置的延时下）会合并为一个更改，因此触发撤销将会撤销多个更改。使用`cutoff()`将会重置合并的更改，因此，在`cutiff()`后的更改将不会被合并。

**方法**

```
cutoff()
```

**例子**

```
quill.history.cutoff();
```

### undo

撤销最后一次更改。

**方法**

```
undo()
```

**例子**

```
quill.history.undo();
```

### redo

如果最后一次更改执行了撤销，那么重做这次撤销。否则什么都不做。

**方法**

```
redo()
```

**例子**

```
quill.history.redo();
```


