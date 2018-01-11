# Editor

## blur

从编辑器中移除焦点。

**Methods**

```
blur()
```

**Examples**

```
quill.blur();
```

## focus

编辑器获取焦点到最后位置。

**Methods**

```
focus()
```

**Examples**

```
quill.focus();
```

## disable

速记`enable(false)`

## enable

设置编辑器能够通过输入设备（如鼠标或键盘）的能力。

**Methods**

```
enable(enabled: boolean = true)
```

**Examples**

```
quill.enable();
quill.enable(false);   // Disables user input
```

## hasFocus

检查编辑器是否存在焦点。注意：工具栏、提示上的焦点，不能算编辑器的焦点。

**Methods**

```
hasFocus(): Boolean
```

**Examples**

```
quill.hasFocus();
```

## update

同步检查编辑器的用户更新和触发事件，如果发生变化。在冲突解决期间需要最新状态的协作用例非常有用。来源可能是‘user’、‘api’或者‘silent’。

**Methods**

```
update(source: String = 'user')
```

**Examples**

```
quill.update();
```




