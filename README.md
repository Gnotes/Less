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
输出：

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

- 变量插值(替换)

```less
@mySelector: banner;

.@{mySelector} {
  font-weight: bold;
  line-height: 40px;
  margin: 0 auto;
}
```
输出：

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
- 变量指针

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
- 延迟加载&声明后置

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

**继承所有** 意思是继承一个样式中所有**嵌套**的样式，通过关键字`all`实现，且这个关键字必须跟在选择器最后

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

其实是在多个样式需要继承同一样式时，可以将`:extend()`放到样式内部(body)，将待继承的选择器样式继承到当前选择器


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

### 混合

> 混合就是将多个样式融合到一起，作为混合的样式作为属性添加到样式中  
> 可以混合 `class` `id` 选择器的样式  
> 混合的样式作为属性添加时，括号为可选项  
> 当样式选择器后跟上括号`()`时，在编译输出结果中不会输出该选择器样式

- 基本混合

```less
.a {
  color: red;
}

#b(){
  color: red;
}

.mixin-class {
  .a;
}

.mixin-id {
  #b();
}
```

输出:

```css
.a {
  color: red;
}
.mixin-class {
  color: red;
}
.mixin-id {
  color: red;
}
```

- 参数传递

> 参数通过 `@paramName [: default]` 指定  
> 多个参数可以通过 逗号 `,` 、 分号 `;` 分隔  
> 当使用了分号分割时，逗号分隔的参数将会被示为 `一组数据`，可以理解为数组参数,如：   
> `.name(1, 2, 3; something, else)` 两个数组形式的参数  
> `.name(1, 2, 3)` 三个数字类型的参数  
> `.name(1, 2, 3;)` 一个数组类型的参数  
> `.name(@param1: red, blue;)` 一个数组形式的参数，并第一个有默认值  


```less
.border-radius(@radius: 5px) {
  border-radius: @radius;
}

#header {
  .border-radius(4px);
}

.button {
  .border-radius();
}
```

输出:

```css
#header {
  border-radius: 4px;
}
.button {
  border-radius: 5px;
}
```

- 混合的重载（类似 Java 概念）

*所谓重载就是名字相同，参数个数不同的的混合*  

> 当出现混合重载时，如果比当前混合`A`参数(n)个数多1的混合`B`(n+1)，B的第n+1个参数如果有默认值，那么会使用B这个混合

```less
.mixin(@color) {
  color-1: @color;
}
.mixin(@color; @padding:2) {
  color-2: @color;
  padding-2: @padding;
}
.mixin(@color; @padding; @margin: 2) {
  color-3: @color;
  padding-3: @padding;
  margin: @margin @margin @margin @margin;
}
.some .selector div {
  .mixin(#008000,20px);
}
```

输出:

```css
.some .selector div {
  color-2: #008000;
  padding-2: 20px;
  color-3: #008000;
  padding-3: 20px;
  margin: 2 2 2 2;
}
```

- 具名混合

> 指定混合参数名称

```less
.mixin(@color: black; @margin: 10px; @padding: 20px) {
  color: @color;
  margin: @margin;
  padding: @padding;
}
.class1 {
  .mixin(@margin: 20px; @color: #33acfe);
}
.class2 {
  .mixin(#efca44; @padding: 40px);
}
```

输出: 

```css
.class1 {
  color: #33acfe;
  margin: 20px;
  padding: 20px;
}
.class2 {
  color: #efca44;
  margin: 10px;
  padding: 40px;
}
```

- @arguments 变量

> @arguments 代表了当前混合的所有参数，类似JS中的 arguments

```less
.box-shadow(@x: 0; @y: 0; @blur: 1px; @color: #000) {
  box-shadow: @arguments;
}
.big-block {
  .box-shadow(2px; 5px);
}
```

输出: 

```css
.big-block {
  box-shadow: 2px 5px 1px #000;
}
```

- 模式匹配

> `@_` 匹配所有

```less
@switch: light;

.mixin(dark; @color) {
  color: darken(@color, 10%);
}
.mixin(light; @color) {
  color: lighten(@color, 10%);
}
.mixin(@_; @color) {
  display: block;
}

.class {
  .mixin(@switch; #888);
}
```

输出:

```css
.class {
  color: #a2a2a2;
  display: block;
}
```



## 参考文档

- [Less 快速入门](http://less.bootcss.com/)

## 在线编辑

- [Less2Css Preview Online](http://less2css.org/)
