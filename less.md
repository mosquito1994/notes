# less学习笔记

## 引入
	
  先引入less文件，再引入下载的less.js文件。也可以使用cdn
  
  ```html
  <link rel="stylesheet/less" type="text/css" href="styles.less">
  <script src="less.js" type="text/javascript"></script>
  ```

## 变量

  1. 定义变量
  
	```css		
	@color: #4d926f;
	@hightlight: color;
	```

  2. 应用到样式或类名中
  
	运用到样式中
	
	```less
	#header {
		color: @@color; 
		/* 可以直接引用@color变量，也可以像这样间接引用，就像js中eval函数一样 */
	}
	```
	则编译后的结果为
	
	```css
	#header {
		color: #4d926f;
	}
	```
	另外运用到类名中
	
	```less
	@mySelector: banner;
	
	.@{mySelector} {
	  font-weight: bold;
	  line-height: 40px;
	  margin: 0 auto;
	}
	```
	编译结果为
	
	```css
	.banner {
	  font-weight: bold;
	  line-height: 40px;
	  margin: 0 auto;
	}
	```
  3. 规则

	基本遵循函数作用域链的变量规则
		
## mixin

  一个类的嵌套，有点像js中的函数
  
```less
.roundedCorners(@radius:5px) {
	-moz-border-radius: @radius;
	-webkit-border-radius: @radius;
	border-radius: @radius;
}
```
	
  括号中的值是没有传入值时候的默认值
  这样就可以在之后引用它：
  
```less
#header {
	.roundedCorners();
}
#footer {
	.roundedCorners(10px);
}
```
	
  编译的结果为
  
```css
#header {
	-moz-border-radius:5px;
	-webkit-border-radius:5px;
	border-radius:5px;
}
#footer {
	-moz-border-radius:10px;
	-webkit-border-radius:10px;
	border-radius:10px;
}
```
	
  当然，也可以不定义任何参数

```less
.wrap(){
	text-wrap: wrap;
	white-space: pre-wrap;
	white-space: -moz-pre-wrap;
	word-wrap: break-word;
}
pre {
	.wrap;
}
```

  编译结果为
  
```css
pre {
	text-wrap: wrap;
	white-space: pre-wrap;
	white-space: -moz-pre-wrap;
	word-wrap: break-word;
}
```

## 嵌套
	
  less允许像dom的嵌套结构一样书写css规则

```less
#header {
	display: inline;
	float: left;
	h1 {
		font-size: 26px;
		font-weight: bold;
		a {
			text-decoration: none;
			color: #f36;
			&:hover {
				text-decoration: underline;
				color: #63f;
			}
		}
	}
	p {
		font-size: 12px;
	}
}
```
	
  每一层大括号都表示一层嵌套。上面编译的结构是这样的：
  
```css
#header {
    display: inline;
    float: left;
}
#header h1 {
    font-size: 26px;
    font-weight: bold;
}
#header h1 a {
    color: #FF3366;
    text-decoration: none;
}
#header h1 a:hover {
    color: #6633FF;
    text-decoration: underline;
}
#header p {
    font-size: 12px;
}
```
	
  可以看到这样的写法非常方便，但是不推荐嵌套太深，否则十分影响css的性能。
  
  对于伪类和后代元素写法的css规则也有特定写法。
  
```less
a {
	color: red;
	text-decoration: none;
	&:hover {
		color: blue;
		text-decoration: underline;
	}
	&.active{
		color: blue;
	}
}
```
	
  编译结果为：
  
```css
a {
	color: red;
	text-decoration: none;
}
a:hover {
	color: blue;
	text-decoration: underline;
}
a.active {
	color: blue;
}
```

## Functions & Operations

  1. less允许对属性值/变量进行加减乘除操作，并允许使用括号改变优先级
  
  	```less
  	@base: 5%;
  	@filler: @base*2;
  	@other: @base + @filler;
  	#header {
  		color: #888 / 4;
  		height: 100% / 2 + @filler; 
  	}
  	```
	编译结果为：
	
	```css
	#header {
	    color: #222222;
	    height: 60%;
	}
	```
  2. less提供多种与颜色变换相关的函数
  
	```less
	lighten(@color, 10%);     // return a color which is 10% *lighter* than @color
	darken(@color, 10%);      // return a color which is 10% *darker* than @color

	saturate(@color, 10%);    // return a color 10% *more* saturated than @color
	desaturate(@color, 10%);  // return a color 10% *less* saturated than @color

	fadein(@color, 10%);      // return a color 10% *less* transparent than @color
	fadeout(@color, 10%);     // return a color 10% *more* transparent than @color

	spin(@color, 10);         // return a color with a 10 degree larger in hue than @color
	spin(@color, -10);        // return a color with a 10 degree smaller hue than @color	
	```
	
## 命名空间
	
```less
#bundle {
  .button () {
    display: block;
    border: 1px solid black;
    background-color: grey;
    &:hover { background-color: white }
  }
  .tab { ... }
  .citation { ... }
}		
```
如果另一段样式与#bundle .button相同，就可以像下面这样直接引入，有点像html中引入外部文件

```less
#header a {
  color: orange;
  #bundle > .button;
}		
```

*大部分例子引用自 http://www.w3cplus.com/css/less
