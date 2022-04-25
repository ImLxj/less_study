## Less预编译语言的学习

### 一、less的安装和使用

#### 	1、less的安装

​		在Node.js环境中使用less：

```
npm install -g less
```

​		在浏览器环境中使用Less：

```html
<!--引入less样式表-->
<link rel="stylesheet/less" type="text/css" href="styles.less" />
<!--在渲染HTML页面时,less文件需要编译成css文件,在服务端,如Node.js有专门编译less的模块。如果是在客户端，需要从less官网下载less.js文件，然后再html页面引入-->
<script src="less.js" type="text/javascript"></script>
```

​		在vscode编辑软件中，下载EASY LESS这个插件，可以不需要引入js文件直接使用less，但是不用引入less文件，直接引入css文件即可。

#### 2、less的使用

​	注释：

```less
// 这种注释编译后不会出现在css文件上
/* 这种注释会出现在css文件上*/
```

​	Less变量：

声明变量通过`@变量名`,并且使用时直接键入@名称，在平时工作中，我们就可以把常用的变量封账到一个文件中，这样利于代码组织和维护。

```less
// 这种注释编译后不会出现在css文件上
/* 这种注释会出现在css文件上*/

@color : red;
@bgColor: pink;
@width : 200px;
@height : 200px;

.box {
  color: @color;
  background-color: @bgColor;
  width: @width;
  height: @height;
}
```

通过Easy Less生成的对应css文件：

```css
/* 这种注释会出现在css文件上*/
.box {
  color: red;
  background-color: pink;
  width: 200px;
  height: 200px;
}
```

### 二、less的变量

##### 1、选择器变量

选择器变量可以直接通过`@名称:类名/id;`、`@名称:(去掉. #)名;`

```less
// 直接通过@引用@myId
@myId: #warp;
// 通过 . # 来引用
@myClass: box;

// 第一种方式
@{myId} {
  color: red;
  width: 100px;
  height: 100px;
}

// 第二种方式
.@{myClass} {
  color: gold;
  width: 100px;
  height: 100px;
}

// 第三种
#@{myClass} {
  font-size: 20px;
}
```

##### 2、属性变量

```less
// 属性变量
@backGroundColor: background-color;
@color: pink;

#@{myClass} {
  font-size: 20px;
  @{backGroundColor}: @color;
}

// 生成的css
#box {
  font-size: 20px;
  background-color: pink;
}
```

##### 3、 url变量

项目结构改变时，修改其变量即可。

```less
// url变量
@images: "../img";

body {
  background: url("@{images}/dog.png");
}

/* 生成的css */
body {
  background: url("../img/dog.png");
}
```

##### 4、声明变量

可以定义重复的属性到里面，直接调用这个变量就可。

```less
// 声明变量
@background: {
	background-color: red;
};

@Rulers: {
	width: 200px;
	height: 200px;
	border: 1px solid red;
};

.@{myClass} {
	@background();
	@Rulers();
}
/* 对应的css */
.box {
  background-color: red;
  width: 200px;
  height: 200px;
  border: 1px solid red;
}
```

##### 5、变量运算

​	-- 加减法时以第一个数据的单位为基准。

​	-- 乘除法时 注意单位要统一。

​	-- 在链接运算的时候，注意添加空格否则可能会出现找不到的报错。

```less
// 变量运算
@width: 100px;
@color: #222;

// 第一种方式
@{myId} {
	color: @color * 2;
	width: @width - 20;
	height: @width - 30;
	margin: (@width - 20) * 5;
}

/* css */
#warp {
  color: #444444;
  width: 80px;
  height: 70px;
  margin: 400px;
}
```

##### 6、变量的作用域

一句话理解就是：`就近原则`

```less
// 变量作用域
@var: @a;
@a: 100px;

@{myId} {
	color: red;
	width: 100px;
	height: 100px;
	margin: @var;
	@a: 20px;
}

/* css */
#warp {
  color: red;
  width: 100px;
  height: 100px;
  margin: 20px;
}
```

##### 7、用变量去替换变量

```less
/* Less */
@font: @var;
@var: "hello world";
#@{myClass}::after {
	content: @var;
	font-size: 20px;
}

/* css */
#box::after {
  content: "hello world";
  font-size: 20px;
}
```

### 三、 less的嵌套

##### 1、&符的妙用

&：代表上一层选择器的名字，此例以box来说明。

```less
// Less
@box: .box;

@{box} {
	width: 100px;
	height: 100px;
	&::after {
		content: "hello world";
	}
	span {
		display: block;
		width: 20px;
		height: 20px;
		background-color: gray;
	}
	&_center { //处理方式直接 & 变成 .box
		color: red;
	}
}

// 生成的css
.box {
  width: 100px;
  height: 100px;
}
.box::after {
  content: "hello world";
}
.box span { // 嵌套了
  display: block;
  width: 20px;
  height: 20px;
  background-color: gray;
}
.box_center { // 没有嵌套
  color: red;
}
```

##### 2、媒体查询

在以往的工作中，我们使用媒体查询，都要把一个元素分开写：

```css
.warp {
	width: 500px;
	height: 20px;
	background-color: lightgreen;
}
@media screen and (max-width: 768px) {
	.warp {
		width: 100px;
	}
}
```

Less提供了一个十分便捷的方式：

```less
// Less
.warp {
	width: 500px;
	height: 20px;
	background-color: lightgreen;
	@media screen {
		@media (max-width: 768px) {
			width: 100px;
		}
	}
}

// 生成的css
.warp {
  width: 500px;
  height: 20px;
  background-color: lightgreen;
}
@media screen and (max-width: 768px) {
  .warp {
    width: 100px;
  }
}

```

唯一的缺点就是每一个元素都会编译出自己的@media声明，并不会合并。

### 四、混合方法

##### 1、 无参数方法

方法犹如声明集合，使用时直接键入名称即可

```less
// 无参数方法
.card() { // 等价于.card
	background-color: lightsalmon;
	width: 500px;
	height: 500px;
}
#warp {
	.card(); // 等价于 .card
}
// 生成的css
#warp {
  background-color: lightsalmon;
  width: 500px;
  height: 500px;
}
```

其中`.card`与`.card()`是等价的。建议写成`.card()`。

要点：

- `.`  与 `#` 作为方法的前缀。
- 方法后写不写 `()` 看个人习惯。

##### 2、默认参数方法

- Less可以使用默认参数，如果没有参数，那么将使用默认参数。
- `@arguments` 犹如 `JS`中的`arguments`指代的数全部参数。
- 传入的参数中必须带着单位。

```less
// Less
// 默认参数方法
.border(@a:10px,@b:20px,@c:50px,@color:#2b4b6b) {
	border: 1px solid @color;
	// @arguments代表传入的所有参数
	box-shadow: @arguments;
}
#warp2 {
	.border();
}

// 生成的css
#warp2 {
  border: 1px solid #2b4b6b;
  box-shadow: 10px 20px 50px #2b4b6b;
}
```

