# 添加Quill到你的构建系统

Quill的每个版本都可以从各种源（包括[NPM](https://www.npmjs.com/package/quill)或者它的[CDN](https://quilljs.com/docs/download/)）构建和使用。然而，可能会出现用户希望从源代码构建Quill的用例，这是应用构建流程的一部分。如果这个想法没有发生在你身上，请不要在意！使用预制版本是使用Quill的最简单方法。

Quill使用Webpack构建，本指南主要针对[Webpack](https://webpack.js.org/concepts/)用户。但是有些原则可能会转化为其他构建环境。

本指南涵盖的所有内容的基本示例都能在[quill-webpack-example](https://github.com/quilljs/webpack-example/)中找到。

## Webpack

你需要将Webpack和适当的loaders作为开发的依赖项添加到你的应用程序。如果你想从源代码生成Parchment，则Typescript编译器也是必须的。

* Quill源码 - `babel-core`，`babel-loader`，`babel-preset-es2015`
* Parchment源码 - `ts-loader`，`typescript`
* SVG图标 - `html-loader`

你的Webpack配置文件还需要设置Quill和Parchment别名，来指定它们各自的入口源文件。除此之外，Webpack将使用NPM中的预建文件，而不是从源代码构建。

## 入口（Entry）

Quill通过构建`quill.js`和`quill.core.js`分发。构建`quill.js`和`core.js`的目的是导入和注册必要的依赖关系。你可能在你的应用程序中使用仅包含你使用的格式、模块或者主题的类似入口。

```
import Quill from 'quill/core';

import Toolbar from 'quill/modules/toolbar';
import Snow from 'quill/themes/snow';

import Bold from 'quill/formats/bold';
import Italic from 'quill/formats/italic';
import Header from 'quill/formats/header';


Quill.register({
  'modules/toolbar': Toolbar,
  'themes/snow': Snow,
  'formats/bold': Bold,
  'formats/italic': Italic,
  'formats/header': Header
});

export default Quill;
```

## 样式表（Stylesheets）

在样式表方面从源代码构建中获益并不多，因为规则可以很容易的被覆盖。然而，`css-loader`的字符前缀在你的应用程序的css文件中包含Quill样式是很有用的。

```
@import "~quill/dist/quill.core.css"

// 重写你的css样式
```

## 例子

作为一个小示例请查看[quill-webpack-wxample](https://github.com/quilljs/webpack-example/)。