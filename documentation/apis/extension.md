# Extension

## debug

静态方法，给定一个日志等级启用日志信息。等级：`error`、`warn`、`log`或者`info`。传入`true`和`log`相同。传入`false`关闭所有日志信息。

**方法**

```
Quill.debug(level: String | Boolean)
```

**例子**

```
Quill.debug('info');
```

## import

静态方法，返回Quill库、格式、模块或者主题。一般来说，路径应该完全映射到Quill的源代码目录结构。除非额外说明，返回的尸体的修改可能破话所需的功能，强烈建议不要修改。

**方法**

```
Quill.import(path): any
```

**例子**

```
var Parchment = Quill.import('parchment');
var Delta = Quill.import('delta');

var Toolbar = Quill.import('modules/toolbar');
var Link = Quill.import('formats/link');
// 类似于ES6语法`import Link from 'quill/formats/link';`
```

## register

用来注册模块、主题或者格式，使其能够可以添加到编辑器中。之后可以通过`Quill.import`进行检索。使用`formats/`、`modules/`或者`themes/`的路径前缀分别注册格式、模块或者主题。相同的路径将会覆盖现有的定义。

**方法**

```
Quill.register(path: String, def: any, supressWarning: Boolean = false)
Quill.register(defs: { [String]: any }, supressWarning: Boolean = false)
```

**例子**

```
var Module = Quill.import('core/module');

class CustomModule extends Module {}

Quill.register('modules/custom-module', Module);
```

```
Quill.register({
  'formats/custom-format': CustomFormat,
  'modules/custom-module-a': CustomModuleA,
  'modules/custom-module-b': CustomModuleB,
});
```

## addContainer

添加并返回一个Quill容器内绑定在编辑器上的容器。按照规定，Quill模块都必须有一个`ql-`的类名前缀。在这个容器插入之前，可以包含一个refNode，这个是可选的。

**方法**

```
addContainer(className: String, refNode?: Node): Element
addContainer(domNode: Node, refNode?: Node): Element
```

**例子**

```
var container = quill.addContainer('ql-custom');
```

## getModule

检索已经添加到编辑器的模块。

**方法**

```
getModule(name: String): any
```

**例子**

```
var toolbar = quill.getModule('toolbar');
```
