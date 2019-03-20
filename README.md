# css-layout
css布局方式

## 前言

在学习[Flex布局实例](http://www.ruanyifeng.com/blog/2015/07/flex-examples.html)时，无意中发现阮一峰老师给出了“圣杯布局”的Flex实现方式，出于好奇查了一下，这就引出了这篇文章（据说双飞翼布局是阿里笔试题）。

## 圣杯布局

首先来看圣杯布局的定义，wikipedia上给出了一个示意图如下。

![圣杯布局](https://upload.wikimedia.org/wikipedia/commons/thumb/a/ad/HolyGrail.svg/1280px-HolyGrail.svg.png)

圣杯布局有如下五个要求：

- 中间列宽度自适应(fluid)，两边列宽度固定
- 在源文档(source)中，中间列处于侧边两列之前
- 任意一列都可以作为最高列
- 除了必要的元素之外，只多出一个div标签
- 用简洁的CSS实现，尽量减少hack

来手动实现一个圣杯布局。

```html
<!DOCTYPE html>
<html lang="en">
<head>
	<meta charset="UTF-8">
	<title>圣杯布局</title>
	<style>
		* {
			margin: 0;
			padding: 0;
		}
		.header	{
			background-color: #cccccc;
		}
		.footer {
			background-color: #cccccc;
		}
		.container {
			overflow: hidden; /* 使container不受float影响，具备高度 */
			padding: 0 200px 0 200px;
		}
		.content {
			background-color: red;
			width: 100%;
			float: left;
			min-height: 100px;
		}
		.left {
			background-color: green;
			width: 200px;
			float: left;
			margin-left: -100%; /* 把左侧副列拉到主列那一行的最左侧 */
			position: relative; /* 使left属性生效 */
			left: -200px;
			min-height: 100px;
		}
		.right {
			background-color: blue;
			width: 200px;
			float: left;
			margin-left: -200px; /* 把右侧副列拉到主列那一行的最右侧 */
			position: relative; /* 使right属性生效 */
			right: -200px;
			min-height: 100px;
		}
	</style>
</head>
<body>
	<div class="header">#header</div>
	<div class="container">
		<div class="content">我是主列，我的内容要优先渲染我是主列，我的内容要优先渲染我是主列，我的内容要优先渲染我是主列，我的内容要优先渲染我是主列，我的内容要优先渲染</div>
		<div class="left">我是左侧副列我是左侧副列我是左侧副列我是左侧副列我是左侧副列我是左侧副列我是左侧副列我是左侧副列我是左侧副列我是左侧副列我是左侧副列我是左侧副列我是左侧副列我是左侧副列我是左侧副列我是左侧副列</div>
		<div class="right">我是右侧扩展列我是右侧扩展列我是右侧扩展列我是右侧扩展列我是右侧扩展列我是右侧扩展列</div>
	</div>
	<div class="footer">#footer</div>
</body>
</html>
```

接下来说明一下圣杯布局的实现思路。

首先要搭建DOM结构，`header`、`container`、`footer`几个元素的顺序是依次排列的，而`container`中有主列首先渲染的需求，因此`container`中依次是`main`、`left`、`right`。因此我们能够得到如下html代码。

```html
<div class="header">#header</div>
<div class="container">
	<div class="content">我是主列，我的内容要优先渲染我是主列，我的内容要优先渲染我是主列，我的内容要优先渲染我是主列，我的内容要优先渲染我是主列，我的内容要优先渲染</div>
	<div class="left">我是左侧副列我是左侧副列我是左侧副列我是左侧副列我是左侧副列我是左侧副列我是左侧副列我是左侧副列我是左侧副列我是左侧副列我是左侧副列我是左侧副列我是左侧副列我是左侧副列我是左侧副列我是左侧副列</div>
	<div class="right">我是右侧扩展列我是右侧扩展列我是右侧扩展列我是右侧扩展列我是右侧扩展列我是右侧扩展列</div>
</div>
<div class="footer">#footer</div>
```

圣杯布局中有一条要求是**除了必要的元素之外，只多出一个div标签**，从上面的DOM结构我们可以看出，`container`即是多出的`div`标签，并且只有这一个`div`标签是多余的，符合了圣杯布局的要求。

有了骨架，紧接着来拼装样式。

首先是三列元素均要实现`float: left;`，这样才能实现更换三列元素的展示位置。将左右两列设置好固定的宽度，再为中间的元素设置`width: 100%;`，这一步也是在为**中间列宽度自适应(fluid)，两边列宽度固定**这一条件做准备。

实现过后的代码及效果如下。

![初步实现](http://4picture.test.upcdn.net/superbed/2019/03/20/5c91d8b83a213b0417c7ae1b.jpg)

```css
.header	{
	background-color: #cccccc;
}
.footer {
	background-color: #cccccc;
}
.main {
	background-color: red;
	width: 100%;
	float: left;
}
.left {
	background-color: green;
	width: 200px;
	float: left;
}
.right {
	background-color: blue;
	width: 200px;
	float: left;
}
```

实现到这里我们能够发现，因为为主列设置了`width: 100%;`，因此左右两列被挤到了第二行，接下来我们要将左右两列移动到我们期望的位置上，而要实现这一点，要依靠负边距来实现，有关负边距的知识可以参考[CSS布局奇淫巧计之-强大的负边距](http://www.cnblogs.com/2050/archive/2012/08/13/2636467.html)。

话不多说，来敲代码。

首先是左侧副列，要将它移动到和主列同一行的最左端，需要设置`margin-left: -100%;`，这是因为此时在浮动布局下三列依次排列，而副列与扩展列出现在第二行是因为主列自己就占了100%的宽度，那么，为副列设置负边距为-100%，就能够将它的位置调整到与主列起始位置相同，也就是我们期望的位置。

然后是右侧的扩展列，左侧的副列已经移走了，现在右侧的扩展列相当于紧挨着主列的末尾，为它设置`,margin-left: -200px;`，其中`200px`的宽度也就是扩展列的宽度，此时可以发现右侧扩展列成功移动到我们期望的位置上。

具体代码与实现效果如下。

![第二步](http://4picture.test.upcdn.net/superbed/2019/03/20/5c91dad83a213b0417c7c79d.jpg)

```css
.main {
	background-color: red;
	width: 100%;
	float: left;
}
.left {
	background-color: green;
	width: 200px;
	float: left;
	margin-left: -100%;
}
.right {
	background-color: blue;
	width: 200px;
	float: left;
	margin-left: -200px;
}
```

看到上图中的实现效果，明显发现`footer`部分的展示是不正常的。通过审查元素看到，`footer`部分已经紧挨着`header`了。

![审查元素](http://4picture.test.upcdn.net/superbed/2019/03/20/5c91dbf83a213b0417c7d216.jpg)

因此我们想到之前整理过的一篇笔记中提到过的，浮动元素导致父元素高度塌陷的问题。笔记可以查看[外边距塌陷及子元素浮动导致的父元素高度塌陷](https://www.yuque.com/docs/share/41f0933a-d6ec-42d3-a635-4c262836f331)。

因此这里我们有两种选择，一种是为`container`设置`overflow: hidden;`，另一种方案是为`footer`设置`clear: both;`，两种方式都能够实现我们的需求，我们暂时使用第二种方式。

代码及具体实现效果如下。

```css
.header	{
	background-color: #cccccc;
}
.footer {
	background-color: #cccccc;
	clear: both;
}
.main {
	background-color: red;
	width: 100%;
	float: left;
}
.left {
	background-color: green;
	width: 200px;
	float: left;
	margin-left: -100%;
}
.right {
	background-color: blue;
	width: 200px;
	float: left;
	margin-left: -200px;
}
```

![第三步](http://4picture.test.upcdn.net/superbed/2019/03/20/5c91dd1a3a213b0417c7dc64.jpg)

进行到这里，页面大致结构已经符合我们的预期了，但我们会发现，主列的内容被左侧的副列遮挡了一部分，因此接下来着手解决这个问题，而**这一步也正是圣杯布局与双飞翼布局的差异所在**。

在这里`container`元素终于要发挥它的作用了。我们为它设置`padding: 0 200px 0 200px;`，这样左右两侧就富余除了副列与扩展列的空间。

![第四步](http://4picture.test.upcdn.net/superbed/2019/03/20/5c91ddea3a213b0417c7e3b2.jpg)

但我们发现，副列和扩展列也一起移动了，因此我们想办法把这两列再次移动一下。

为副列设置`left: -200px;`，为扩展列设置`right: -200px;`，刷新页面，却发现没有生效。因此我们想到，是不是应该为这两个元素设置`relative`布局呢，来尝试一下。

> 有关relative布局的内容可以查看[【前端Talkking】CSS系列——CSS深入理解之relative定位](https://segmentfault.com/a/1190000014877050)，讲解的十分详细。

代码及实现效果如下。

```css
.header	{
	background-color: #cccccc;
}
.footer {
	background-color: #cccccc;
	clear: both;
}
.container {
	padding: 0 200px 0 200px;
}
.main {
	background-color: red;
	width: 100%;
	float: left;
}
.left {
	background-color: green;
	width: 200px;
	float: left;
	margin-left: -100%;
	left: -200px;
	position: relative;
}
.right {
	background-color: blue;
	width: 200px;
	float: left;
	margin-left: -200px;
	right: -200px;
	position: relative;
}
```

![实现效果](http://4picture.test.upcdn.net/superbed/2019/03/20/5c91ded43a213b0417c7eb8a.jpg)

到此，我们就实现了一个代码最简的圣杯布局。回顾一下圣杯布局的五个要求，在这一过程中均被满足了~

如果希望三列元素有一个最小高度，可以为它们设置一个`min-height`，这里就不做演示了。

> 补充一点，因为为每个元素设置了背景色，可以明显看到实现到这里，三列的高度是不相同的，如果有相同高度的需求，可以参考[几种常见的CSS布局](https://juejin.im/post/5bbcd7ff5188255c80668028#heading-18)文章中利用正padding+负margin实现的等高布局（但这种方式的优劣性有待考究），并且如果要使用这一方式使其等高，在清除浮动导致的正常布局影响那一步，应当选择第一种方法，也就是为`container`设置`overflow: hidden;`。

## 双飞翼布局

上面实现圣杯布局中，我们提到了双飞翼布局与圣杯布局的差异之处，也就是如何使主列被遮挡的元素不再被遮挡。

圣杯布局的思路是，为外层容器提供一个padding，并将副列和扩展列移动至期望位置上。而双飞翼布局的实现思路是，在主列中再嵌套一个`div`，在该`div`中放置主列的内容，然后为该div设置marin，来使其避开副列和扩展列的遮挡。

代码及实现效果如下。

```html
<!DOCTYPE html>
<html lang="en">
<head>
	<meta charset="UTF-8">
	<title>双飞翼布局</title>
	<style>
		* {
			margin: 0;
			padding: 0;
		}
		.header	{
			background-color: #cccccc;
		}
		.footer {
			background-color: #cccccc;
		}
		.container {
			overflow: hidden; /* 使container不受float影响，具备高度 */
		}
		.main {
			background-color: red;
			width: 100%;
			float: left;
			min-height: 100px;
		}
		.main-inner {
			margin: 0 200px 0 200px;
		}
		.left {
			background-color: green;
			width: 200px;
			float: left;
			margin-left: -100%; /* 把左侧副列拉到主列那一行的最左侧 */
			min-height: 100px;
		}
		.right {
			background-color: blue;
			width: 200px;
			float: left;
			margin-left: -200px; /* 把右侧副列拉到主列那一行的最右侧 */
			min-height: 100px;
		}
	</style>
</head>
<body>
	<div class="header">#header</div>
	<div class="container">
		<div class="main">
			<div class="main-inner">
				我是主列，我的内容要优先渲染我是主列，我的内容要优先渲染我是主列，我的内容要优先渲染我是主列，我的内容要优先渲染我是主列，我的内容要优先渲染
			</div>
		</div>
		<div class="left">我是左侧副列我是左侧副列我是左侧副列我是左侧副列我是左侧副列我是左侧副列我是左侧副列我是左侧副列我是左侧副列我是左侧副列我是左侧副列我是左侧副列我是左侧副列我是左侧副列我是左侧副列我是左侧副列</div>
		<div class="right">我是右侧扩展列我是右侧扩展列我是右侧扩展列我是右侧扩展列我是右侧扩展列我是右侧扩展列</div>
	</div>
	<div class="footer">#footer</div>
</body>
</html>
```

![双飞翼-实现效果](http://4picture.test.upcdn.net/superbed/2019/03/20/5c91e1943a213b0417c80463.jpg)

可以看到，双飞翼布局和圣杯布局实现出来的最终效果实际上是相同的，但它们的实现理念不同。双飞翼布局比圣杯布局多使用了一个无意义的DOM节点，会造成渲染树计算量的增加。而圣杯布局也有其缺点，那就是center部分的最小宽度不能小于left部分的宽度，否则会left部分掉到下一行，造成布局的混乱如下图。

![异常情况](http://4picture.test.upcdn.net/superbed/2019/03/20/5c91e2963a213b0417c80d49.jpg)

而双飞翼布局不会有这种情况，能够保证布局的稳定性，如下图。

![双飞翼-副列过宽](http://4picture.test.upcdn.net/superbed/2019/03/20/5c91e2cb3a213b0417c80f9b.jpg)

以上就是两种常见的布局~

## 圣杯布局的Flex实现方式

前言里提到过，之所以有了这篇文章是在练习阮一峰老师的Flex布局实例时顺藤摸瓜发现的。绕了这么一大圈，回到最初的问题上，如何使用Flex布局实现一个圣杯布局？

先不看答案，自己动手试试。

```html
<!DOCTYPE html>
<html lang="en">
<head>
	<meta charset="UTF-8">
	<title>Flex实现圣杯布局</title>
	<style>
		* {
			margin: 0;
			padding: 0;
		}
		.header	{
			background-color: #cccccc;
		}
		.footer {
			background-color: #cccccc;
		}
		.container {
			display: flex;
		}
		.main {
			background-color: red;
			flex: 1;
		}
		.left {
			background-color: green;
			flex-basis: 200px;
			order: -1;
		}
		.right {
			background-color: blue;
			flex-basis: 200px;
		}
	</style>
</head>
<body>
	<div class="header">#header</div>
	<div class="container">
		<div class="main">
			我是主列，我的内容要优先渲染我是主列，我的内容要优先渲染我是主列，我的内容要优先渲染我是主列，我的内容要优先渲染我是主列，我的内容要优先渲染
		</div>
		<div class="left">我是左侧副列我是左侧副列我是左侧副列我是左侧副列我是左侧副列我是左侧副列我是左侧副列我是左侧副列我是左侧副列我是左侧副列我是左侧副列我是左侧副列我是左侧副列我是左侧副列我是左侧副列我是左侧副列</div>
		<div class="right">我是右侧扩展列我是右侧扩展列我是右侧扩展列我是右侧扩展列我是右侧扩展列我是右侧扩展列</div>
	</div>
	<div class="footer">#footer</div>
</body>
</html>
```

上面的代码实现出的效果如下图。

![flex实现效果](http://4picture.test.upcdn.net/superbed/2019/03/20/5c91eb843a213b0417c86a68.jpg)

来看最终答案。

```html
<body class="HolyGrail">
  <header>...</header>
  <div class="HolyGrail-body">
    <main class="HolyGrail-content">...</main>
    <nav class="HolyGrail-nav">...</nav>
    <aside class="HolyGrail-ads">...</aside>
  </div>
  <footer>...</footer>
</body>
```

```css
.HolyGrail {
  display: flex;
  min-height: 100vh;
  flex-direction: column;
}

header,
footer {
  flex: 1;
}

.HolyGrail-body {
  display: flex;
  flex: 1;
}

.HolyGrail-content {
  flex: 1;
}

.HolyGrail-nav, .HolyGrail-ads {
  /* 两个边栏的宽度设为12em */
  flex: 0 0 12em;
}

.HolyGrail-nav {
  /* 导航放到最左边 */
  order: -1;
}
```

好吧，果然还是有差距的。

我们自己的实现方式中有哪些可优化的点和不足之处呢？

首先是，圣杯布局规定左右两列一定是定宽，但我们的视线中，只为左右两列添加`flex-basis: 200px;`这一个属性，意思是左右两列在主轴上计算占有空间及进行缩放时依据的基础宽度是200px，但是，`flex-shrink`属性的默认值是1，也就是说，当主轴空间不足时，默认会缩放这两列。而使用`flex: 0 0 200px;`，不仅便于浏览器计算，而且能够保证这两列不会被缩放。

> 补充一点，上边说的主轴空间不足时缩放左右两列的情况，只会在左右两列宽度很宽的时候出现。

其次是，我们没有在外层使用Flex布局，因此其高度是由文本高度决定的。可以将整个body设置为Flex布局，设置最小高度为100vh，然后分别设置`header`、`container`、`footer`的flex属性（阮一峰老师的案例中简单的给了三个1，我们可以根据自己的需要调整高度），这样能够实现该布局占满整个浏览器窗口，而内容过长时又可以竖向滚动的效果。

以上就是如何使用Flex布局实现圣杯布局~

## 参考文章

[几种常见的CSS布局](https://juejin.im/post/5bbcd7ff5188255c80668028#heading-8)

[CSS布局 -- 圣杯布局 & 双飞翼布局](https://www.cnblogs.com/imwtr/p/4441741.html)

[CSS布局之--淘宝双飞翼布局](http://www.cnblogs.com/langzs/archive/2013/01/27/taobaoshuangfeiyi.html)

[CSS布局奇淫巧计之-强大的负边距](http://www.cnblogs.com/2050/archive/2012/08/13/2636467.html)

[圣杯布局&双飞翼布局认识](https://blog.csdn.net/u013778905/article/details/51684395)

[双飞翼布局介绍-始于淘宝UED](http://www.imooc.com/wenda/detail/254035)

[CSS布局--圣杯布局和双飞翼布局以及使用Flex实现圣杯布局](https://segmentfault.com/a/1190000013867208)