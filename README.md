# Less

## 目录

- [概述](#概述)
- [变量](#变量)
- [继承](#继承)


### 概述

> Less 是css的一种扩展，提供了各种函数编程式的语法，以更为简便的书写方式编写css

其主要包含了 `变量` `继承` `混合` `嵌套` `运算` `函数` `作用域` `条件控制` `合并` `父选择器` 以及内置的工具函数等...

### 变量

规则： `@variableName : expression or value`

- 变量定义

```less
@link-color:        #428bca;
@link-color-hover:  darken(@link-color, 10%);

a,
.link {
  color: @link-color;
}
a:hover {
  color: @link-color-hover;
}
.widget {
  color: #fff;
  background: @link-color;
}
```
输出：

```css
a,
.link {
  color: #428bca;
}
a:hover {
  color: #3071a9;
}
.widget {
  color: #fff;
  background: #428bca;
}
```

- 变量插值(替换)

```less
@mySelector: banner;

.@{mySelector} {
  font-weight: bold;
  line-height: 40px;
  margin: 0 auto;
}
```
输出：

```css
.banner {
  font-weight: bold;
  line-height: 40px;
  margin: 0 auto;
}
```

- 作为属性

```less
@property: color;

.widget {
  @{property}: #0ee;
  background-@{property}: #999;
}
```

输出:

```css
.widget {
  color: #0ee;
  background-color: #999;
}
```
- 变量指针

*自己取的名字，其实就是使用变量的值再去定义（指向）另一个变量*

```less
@fnord:  "I am fnord.";
@var:    "fnord";

.var-pointer {
	content: @@var;
}
```
输出:

```css
.var-pointer {
  content: "I am fnord.";
}
```
- 延迟加载&声明后置

less 变量由于需要编译的，因此是延迟加载的并且允许先使用后声明

```less
.lazy-eval {
  width: @var;
}

@var: @a;
@a: 9%;
```

输出:

```css
.lazy-eval {
  width: 9%;
}
```

- 作用域（就近原则）

```less
.lazy-eval-scope {
  width: @var;
  @a: 9%;
}

@var: @a;
@a: 100%;
```
输出:

```css
.lazy-eval-scope {
  width: 9%;
}
```

```less
@var: red;

#page {
  #header {
    color: @var; // white
  }
  @var: white;
}
```

输出

```css
#page #header {
  color: white;
}
```

### 继承

*样式的继承通过 `:extend(selector)` 语法来实现*

- 简单继承

```less
nav ul {
  &:extend(.inline);
  background: blue;
}
.inline {
  color: red;
}
```

输出:

```css
nav ul {
  background: blue;
}
.inline,
nav ul {
  color: red;
}
```

- 继承所有

**继承所有** 意思是继承一个样式中所有**嵌套**的样式，通过关键字`all`实现，且这个关键字必须跟在选择器最后

```less
.inline {
  color: red;
  .nested {
  	font-size: 12px;
  }
}

.outter:extend(.inline) {
  width:30px;
}
```

输出:

```css
.inline,
.outter {
  color: red;
}
.inline .nested {
  font-size: 12px;
}
.outter {
  width: 30px;
}
```
比较

```less
.inline {
  color: red;
  .nested {
  	font-size: 12px;
  }
}

.outter:extend(.inline all) {
  width:30px;
}
```

输出:

```css
.inline,
.outter {
  color: red;
}
.inline .nested,
.outter .nested {
  font-size: 12px;
}
.outter {
  width: 30px;
}
```
- 继承指定选择器

```less
div {
  color:#FFF;
  
  pre{
  	color:#F00;
  }
}

pre:hover:extend(div pre){}
```

输出: 

```css
div {
  color: #FFF;
}

div pre,
pre:hover {
  color: #F00;
}
```

- 多继承

```less
div {
  color:#FFF;
  pre{
  	color:#F00;
  }
}

.bucket tr{
  font-size:12px;
}

pre:hover:extend(div pre):extend(.bucket tr){}
// 多个继承可以使用逗号隔开
pre:hover:extend(div pre,.bucket tr){}
```

输出:

```css
div {
  color: #FFF;
}
div pre,
pre:hover {
  color: #F00;
}
.bucket tr,
pre:hover {
  font-size: 12px;
}
```

- 作为属性继承

其实是在多个样式需要继承同一样式时，可以将`:extend()`放到样式内部(body)，将待继承的选择器样式继承到当前选择器


```less
div {
  color:#FFF;
  pre{
  	color:#F00;
  }
}

pre:hover,
.some-class {
  &:extend(div pre);
}
```

输出:

```css
div {
  color: #FFF;
}

div pre,v
pre:hover,
.some-class {
  color: #F00;
}
```



## 参考文档

- [Less 快速入门](http://less.bootcss.com/)

## 在线编辑

- [Less2Css Preview Online](http://less2css.org/)