#sass

##简介

###预处理器比较

同类型的预处理器：sass、less、styleus

CSS 预处理器是一种语言用来为 CSS 增加一些编程的的特性，无需考虑浏览器的兼容性问题，例如你可以在 CSS 中使用变量、简单的程序逻辑、函数等等在编程语言中的一些基本技巧，可以让你的 CSS 更见简洁，适应性更强，代码更直观等诸多好处。

###文件名后缀

sass有两种后缀名文件：
sass3.0前使用缩进，后缀名为sass，不使用大括号和分号；
另一种就是我们这里使用的scss文件（sass3.0后），这种和我们平时写的css文件格式差不多，使用大括号和分号。

```sass
//文件后缀名为sass的语法
body
  background: #eee
  font-size:12px
p
  background: #0982c1

//文件后缀名为scss的语法  
body {
  background: #eee;
  font-size:12px;
}
p{
  background: #0982c1;
} 
```

##变量

sass的变量必须是$开头，后面紧跟变量名，而变量值和变量名之间就需要使用冒号(:)分隔开（就像CSS属性设置一样），如果值后面加上!default则表示默认值。

###普通变量

定义之后可以在全局范围内使用。

```sass
//sass style
//-------------------------------
$fontSize: 12px;
body{
    font-size:$fontSize;
}
```

###默认变量

sass的默认变量仅需要在值后面加上!default即可。

```sass
//sass style
//-------------------------------
$baseLineHeight:        1.5 !default;
body{
    line-height: $baseLineHeight; 
}
```

sass的默认变量一般是用来设置默认值，然后根据需求来覆盖的，覆盖的方式也很简单，只需要在默认变量之前重新声明下变量即可

```sass
//sass style
//-------------------------------
$baseLineHeight:        2;
$baseLineHeight:        1.5 !default;
body{
    line-height: $baseLineHeight; 
}
```

默认变量的价值在进行组件化开发的时候会非常有用。

###特殊变量

一般我们定义的变量都为属性值，可直接使用，但是如果变量作为属性或在某些特殊情况下等则必须要以#{$variables}形式使用。

```sass
//sass style
//-------------------------------
$borderDirection:       top !default; 
$baseFontSize:          12px !default;
$baseLineHeight:        1.5 !default;

//应用于class和属性
.border-#{$borderDirection}{
  border-#{$borderDirection}:1px solid #ccc;
}
//应用于复杂的属性值
body{
    font:#{$baseFontSize}/#{$baseLineHeight};
}
```

###多值变量

多值变量分为list类型和map类型，简单来说list类型有点像js中的数组，而map类型有点像js中的对象。

####list

list数据可通过空格，逗号或小括号分隔多个值，可用`nth($var,$index)`取值。
关于list数据操作还有很多其他函数如length($list)，join($list1,$list2,[$separator])，append($list,$value,[$separator])等，具体可参考sass Functions（搜索List Functions即可）

**定义：**

```sass
//一维数据
$px: 5px 10px 20px 30px;

//二维数据，相当于js中的二维数组
$px: 5px 10px, 20px 30px;
$px: (5px 10px) (20px 30px);
```

**使用：**

```sass
//sass style
//-------------------------------
$linkColor:         #08c #333 !default;//第一个值为默认值，第二个鼠标滑过值
a{
  color:nth($linkColor,1);

  &:hover{
    color:nth($linkColor,2);
  }
}
```

####map

map数据以key和value成对出现，其中value又可以是list。
格式为：$map: (key1: value1, key2: value2, key3: value3);。可通过map-get($map,$key)取值。
关于map数据还有很多其他函数如map-merge($map1,$map2)，map-keys($map)，map-values($map)等，具体可参考sass Functions（搜索Map Functions即可）

**定义：**

```sass
$heading: (h1: 2em, h2: 1.5em, h3: 1.2em);
```

**使用：**

```sass
//sass style
//-------------------------------
$headings: (h1: 2em, h2: 1.5em, h3: 1.2em);
@each $header, $size in $headings {
  #{$header} {
    font-size: $size;
  }
}
```

###全局变量

在变量值后面加上 `!global` 即为全局变量。这个目前还用不上，不过将会在sass 3.4后的版本中正式应用。目前的sass变量范围饱受诟病，所以才有了这个全局变量。

**目前变量机制**

在选择器中声明的变量会覆盖外面全局声明的变量。(这也就人们常说的sass没有局部变量)

```sass
//sass style
//-------------------------------
$fontSize:      12px;
body{
    $fontSize: 14px;        
    font-size:$fontSize;
}
p{
    font-size:$fontSize;
}
```

关于变量的详细分析请查阅：[sass揭秘之变量](http://www.w3cplus.com/preprocessor/sass-basic-variable.html)


##嵌套

sass可以进行选择器的嵌套，表示层级关系，看起来很优雅整齐。分选择器嵌套和属性嵌套

**要点:**

1. `&` 表示父元素选择器
2. `@at-root` 用来跳出选择器嵌套，多个选择器跳出使用 `{}` 包裹起来
3. `@at-root (without: ...)` 和 `@at-root (with: ...)`


默认@at-root只会跳出选择器嵌套，而不能跳出@media或@support，如果要跳出这两种，则需使用@at-root (without: media)，@at-root (without: support)。这个语法的关键词有四个：all（表示所有），rule（表示常规css），media（表示media），support（表示support，因为@support目前还无法广泛使用，所以在此不表）。我们默认的@at-root其实就是@at-root (without:rule)。

[DEMO](http://sassmeister.com/gist/e0e6263447899bfb565e)

##导入

**语法:**

```sass
@import "function.css";
@import "reset.css";
@import "animate.css";
```

**要点:**

1. 文件名命名：_reset.css
2. 导入顺序
3. 与css的import导入规则区别


##注释

1. `/* */` 此注释同css注释，编译时会被一起编译到css中
2. `//` 此注释不会被编译至css


##混合器

sass中使用@mixin声明混合，可以传递参数，参数名以$符号开始，多个参数以逗号分开，也可以给参数设置默认值。声明的@mixin通过@include来调用。

**语法:**

```sass
@mixin box-sizing ($sizing) {
	-webkit-box-sizing:$sizing;     
       -moz-box-sizing:$sizing;
            box-sizing:$sizing;
}
```

**要点:**

1. 参数可传可不传

###@content

**用法：**

```sass
//sass style
//-------------------------------                     
@mixin max-screen($res){
  @media only screen and ( max-width: $res )
  {
    @content;
  }
}
@include max-screen(480px) {
  body { color: red }
}

//css style
//-------------------------------
@media only screen and (max-width: 480px) {
  body { color: red }
}                     
```

> @mixin通过@include调用后解析出来的样式是以拷贝形式存在的，而下面的继承则是以联合声明的方式存在的，所以从3.2.0版本以后，建议传递参数的用@mixin，而非传递参数类的使用下面的继承%。


##继承

sass中，选择器继承可以让选择器继承另一个选择器的所有样式，并联合声明。使用选择器的继承，要使用关键词@extend，后面紧跟需要继承的选择器。

**语法:**

```sass
.success {
	@extend .message;
	border-color: green;
}
```

**要点:**

1. 与想象中的有出入，代码更简洁

###占位选择器%

从sass 3.2.0以后就可以定义占位选择器%。这种选择器的优势在于：如果不调用则不会有任何多余的css文件，避免了以前在一些基础的文件中预定义了很多基础的样式，然后实际应用中不管是否使用了@extend去继承相应的样式，都会解析出来所有的样式。占位选择器以%标识定义，通过@extend调用。

> 在@media中暂时不能@extend @media外的代码片段，以后将会可以。


##运算


##数据类型

SassScript 支持六种不同的数据类型：

- 数字，1, 2, 13, 10px；
- 字符串，有引号字符串或无引号字符串，"foo", 'bar', baz；
- 颜色，blue, #04a3f9, rgba(255,0,0,0.5)；
- 布尔型，true, false；
- 空值，null；
- 值列表，用空格或者逗号分开，1.5em 1em 0 2em, Helvetica, Arial, sans-serif。

> SassScript 也支持其他 CSS 属性值（property value），比如 Unicode 范围，或 !important声明。然而，Sass 不会特殊对待这些属性值，一律视为无引号字符串 (unquoted strings)。


##函数

`@function` 

关于@mixin，%，@function更多说明可参阅：<br>
[sass揭秘之@mixin，%，@function](http://www.w3cplus.com/preprocessor/sass-mixins-function-placeholder.html) <br>
[Sass基础——颜色函数](http://www.w3cplus.com/preprocessor/sass-color-function.html) <br>
[Sass基础——Sass函数](http://www.w3cplus.com/preprocessor/sass-other-function.html) <br>
[sass函数](http://sass-lang.com/documentation/Sass/Script/Functions.html)


##条件判断及循环

###@if判断

@if可一个条件单独使用，也可以和@else结合多条件使用

```sass
//sass style
//-------------------------------
$lte7: true;
$type: monster;
.ib{
    display:inline-block;
    @if $lte7 {
        *display:inline;
        *zoom:1;
    }
}
p {
  @if $type == ocean {
    color: blue;
  } @else if $type == matador {
    color: red;
  } @else if $type == monster {
    color: green;
  } @else {
    color: black;
  }
}
```

###三目判断

语法为：if($condition, $if_true, $if_false) 。三个参数分别表示：条件，条件为真的值，条件为假的值。

```sass
if(true, 1px, 2px) => 1px
if(false, 1px, 2px) => 2px
```

###for循环

for循环有两种形式，分别为：`@for $var from <start> through <end>` 和 `@for $var from <start> to <end>`。$i表示变量，start表示起始值，end表示结束值，这两个的区别是关键字through表示包括end这个数，而to则不包括end这个数。

```sass
//sass style
//-------------------------------
@for $i from 1 through 3 {
  .item-#{$i} { width: 2em * $i; }
}
```

###@each循环

语法为：`@each $var in <list or map>`。其中$var表示变量，而list和map表示list类型数据和map类型数据。sass 3.3.0新加入了多字段循环和map数据循环。

**单个字段list数据循环**

```sass
//sass style
//-------------------------------
$animal-list: puma, sea-slug, egret, salamander;
@each $animal in $animal-list {
  .#{$animal}-icon {
    background-image: url('/images/#{$animal}.png');
  }
}

```

**多个字段list数据循环**

```sass
//sass style
//-------------------------------
$animal-data: (puma, black, default),(sea-slug, blue, pointer),(egret, white, move);
@each $animal, $color, $cursor in $animal-data {
  .#{$animal}-icon {
    background-image: url('/images/#{$animal}.png');
    border: 2px solid $color;
    cursor: $cursor;
  }
}
```

**多个字段map数据循环**

```sass
//sass style
//-------------------------------
$headings: (h1: 2em, h2: 1.5em, h3: 1.2em);
@each $header, $size in $headings {
  #{$header} {
    font-size: $size;
  }
}
```

关于循环判断详细分析请查阅：[sass揭秘之@if，@for，@each](http://www.w3cplus.com/preprocessor/sass-advanced-application.html)


























