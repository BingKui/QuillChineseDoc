# 修改Quill format 默认白名单

**导入相应包**

```
// 设置字体大小白名单
const SizeStyle = Quill.import('attributors/style/size');
```

**修改白名单的值**

```
SizeStyle.whitelist = FontSize.map(item => item.value);
```

**重新绑定到Quill上**

```
Quill.register(SizeStyle);
```