---
title: CSS基础语法
published: 2023-07-06
pinned: false
description: CSS规则由选择器和声明构成
tags: [前端, CSS]
category: 学习项目
draft: false
---

# 语法
CSS规则由 **选择器** 和 一条或多条 **声明** 构成
* 选择器指定需要改变样式的HTML元素
* 声明由 **属性** 和 **值** 构成
	* 属性是样式的名称
	* 值是对应的程度
	声明之间用分号隔开，属性和值之间用冒号隔开
### 内部样式示例
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Document</title>
</head>

<style>
    p{
        color: red;
        font-size: 30px; }
</style> 

<body>
    <p>
        这是一个红色的段落
    </p>
    
    <p>
        这不应该是一条红色的段落,但是P标签已经被设定为红色
    </p>
</body>
</html>
```

### 内联样式示例
```html
<body>
    <p style="color: red;">
        这是一个红色的段落
    </p>
    <p style="background-color: aqua;font-size: 30px;">
        这不是一条红色的段落,并且它有蓝色的底和更大的字体
    </p>
</body>
```

# 外部样式
`<link rel = "stylesheet" type = "text/css" href = "xxx.css"`
