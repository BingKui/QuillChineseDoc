# 构建自定义模块

Quill作为编辑器的核心优势在于其丰富的API和强大的自定义功能。当你在Quill API的基础上实现功能时，把它作为一个模块进行组织是很方便的。本指南的目的在于，我们通过一种方式来构建文字计数器模块，这是许多文字处理器中常见的功能。

> 注意：好多Quill的内部模块的功能都是有组织的。你可以通过实现自己的方法来覆盖这些默认模块，并使用相同的名称进行注册。

## 字数

字数计数器的核心就是统计编辑器中的字数，并在某个用户界面中显示该值。因此我们需要：

1. 监听Quill的文本变化
2. 计算字符的总数
3. 显示这个值

让我们直接跳到一个真实的例子！

**html代码**

```
<div id="editor"></div>
<div id="counter">0</div>
```

**css代码**

```
body {
  padding: 25px;
}

#editor {
  border: 1px solid #ccc;
}

#counter {
  border: 1px solid #ccc;
  border-width: 0px 1px 1px 1px;
  color: #aaa;
  padding: 5px 15px;
  text-align: right;
}
```

**js代码**

```
// Implement and register module
Quill.register('modules/counter', function(quill, options) {
  var container = document.querySelector('#counter');
  quill.on('text-change', function() {
    var text = quill.getText();
    // There are a couple issues with counting words
    // this way but we'll fix these later
    container.innerText = text.split(/\s+/).length;
  });
});

// We can now initialize Quill with something like this:
var quill = new Quill('#editor', {
  modules: {
    counter: true
  }
});
```

**结果**

[计数例子](https://codepen.io/quill/pen/bZkWKA)

这就是为Quill添加一个自定义模块所需的一切！一个函数可以被注册为一个模块，它将被传递到相应的Quill编辑器对象通过配置项。

## 使用配置项

模可以通过传递一个配置对象，来调整所需的行为。我们可以使用它来接收计数器容器的选择器，而不是硬编码的字符串。让我们定制计数器来计算单词或者字符。

**html代码**

```
<div id="editor"></div>
<div id="counter">0 words</div>
```

**css代码**

```
body {
  padding: 25px;
}

#editor {
  border: 1px solid #ccc;
}

#counter {
  border: 1px solid #ccc;
  border-width: 0px 1px 1px 1px;
  color: #aaa;
  padding: 5px 15px;
  text-align: right;
}
```

**js代码**

```
Quill.register('modules/counter', function(quill, options) {
  var container = document.querySelector(options.container);
  quill.on('text-change', function() {
    var text = quill.getText();
    if (options.unit === 'word') {
      container.innerText = text.split(/\s+/).length + ' words';
    } else {
      container.innerText = text.length + ' characters';
    }
  });
});

var quill = new Quill('#editor', {
  modules: {
    counter: {
      container: '#counter',
      unit: 'word'
    }
  }
});
```

**结果**

[计算单词数](https://codepen.io/quill/pen/OXqmEp)

## 构造函数

由于任何函数都可以注册为一个Quill模块，所以我们可以实现我们的计数器作为ES5构造函数或者ES6类。这使我们可以直接访问和使用模块。

**html代码**

```
<div id="editor"></div>
<div id="counter">0 words</div>
```

**css代码**

```
body {
  padding: 25px;
}

#editor {
  border: 1px solid #ccc;
}

#counter {
  border: 1px solid #ccc;
  border-width: 0px 1px 1px 1px;
  color: #aaa;
  padding: 5px 15px;
  text-align: right;
}
```

**js代码**

```
var Counter = function(quill, options) {
  this.quill = quill;
  this.options = options;
  var container = document.querySelector(options.container);
  var _this = this;
  quill.on('text-change', function() {
    var length = _this.calculate();
    container.innerText = length + ' ' + options.unit + 's';
  });
};

Counter.prototype.calculate = function() {
  var text = this.quill.getText();
  if (this.options.unit === 'word') {
    return text.split(/\s+/).length;
  } else {
    return text.length;
  }
};

Quill.register('modules/counter', Counter);

var quill = new Quill('#editor', {
  modules: {
    counter: {
      container: '#counter',
      unit: 'word'
    }
  }
});

var counter = quill.getModule('counter');

// We can now access calculate() directly
console.log(counter.calculate(), 'words');
```

**结果**

[构造函数例子](https://codepen.io/quill/pen/BzbRVR)

## 包装起来

现在让我们整理我们的ES6模块并且修改一些bug。下面就是所有代码了！

**html代码**

```
<div id="editor"></div>
<div id="counter"></div>
```

**css代码**

```
body {
  font-family: Helvetica, Arial, sans-serif;
  font-size: 13px;
  padding: 25px;
}

#editor {
  border: 1px solid #ccc;
}

#counter {
  border: 1px solid #ccc;
  border-width: 0px 1px 1px 1px;
  color: #aaa;
  padding: 5px 15px;
  text-align: right;
}
```

**js代码（Babel）**

```
class Counter {
  constructor(quill, options) {
    this.quill = quill;
    this.options = options;
    this.container = document.querySelector(options.container);
    quill.on('text-change', this.update.bind(this));
    this.update();  // Account for initial contents
  }

  calculate() {
    let text = this.quill.getText();
    if (this.options.unit === 'word') {
      text = text.trim();
      // Splitting empty text returns a non-empty array
      return text.length > 0 ? text.split(/\s+/).length : 0;
    } else {
      return text.length;
    }
  }
  
  update() {
    var length = this.calculate();
    var label = this.options.unit;
    if (length !== 1) {
      label += 's';
    }
    this.container.innerText = length + ' ' + label;
  }
}

Quill.register('modules/counter', Counter);

var quill = new Quill('#editor', {
  modules: {
    counter: {
      container: '#counter',
      unit: 'word'
    }
  }
});
```

**结果**

[最后结果](https://codepen.io/quill/pen/wWOdXZ)