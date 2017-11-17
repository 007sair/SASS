# sass学习

> [sass库传送门](https://github.com/007sair/hero/tree/master/src/css/base)

## 一、变量

#### 命名： `$`

#### 规则：

- 作用域：作用域同javascript，想调用局部变量可在值后使用 `!global` 
- 默认值：变量没有设置具体值时，使用 `!default`

<!-- more -->

```scss
//demo
$primaryColor: #eeccff;
$firstValue: 62.5%;
$firstValue: 24px !default;
body {
  $primaryColor: #ccc;
  background: $primaryColor; //编译后#ccc
  font-size: $firstValue; //编译后62.5%;
}
p {
  color: $primaryColor; //编译后#eeccff
}
```

#### 插值：

变量除了作为属性值使用，还能用在选择器或者属性名上：

```scss
//scss
$out: margin;
$in: padding;
.#{$out}-div{
  #{$out}-top : 10px;
}
.#{$in}-div{
  #{$in}-top : 10px;
}

//css
.margin-div {
    margin-top: 10px;
}
.padding-div {
    padding-top: 10px;
}

```


## 二、引用

#### @import

语法格式如下：

```scss
@import "reset.scss";
//or
@import "reset";
```

引用的scss文件会被自动编译成对应的css文件。如：`reset.scss`会被自动编译成 `reset.css`，解决这个问题可以给`reset.scss`文件名重命名为：`_reset.scss`。<br>
当然，`import`的引用里不需要加`_`。


## 三、注释

sass共有两种注释和一种特殊注释：

- `/*...*/` 这种注释会保留到编译后的css文件中；
- `//...` 这种注释只保留在sass中，不会被编译至css。
- `/*! ... */` 这种注释多了个感叹号，表示为_重要注释_，即使是压缩模式，也会保留这行注释，通常用于声明版权信息。


## 四、嵌套

#### 选择器嵌套

```scss
div{
  h1{
    color: #f00;
  }
}
//或者：
div h1{
  color: #f00
}
```

#### 属性嵌套

属性嵌套可以用在单属性和复合属性的嵌套中：

```scss
//scss
div{
  border: { //注意border后面的冒号
    color: #ccc;
    style: solid;
  }
}

//css
div {
  border-color: #ccc;
  border-style: solid;
}

```

引用父选择器可以通过 `&` 符合实现：

```scss
//scss
a.myAnchor {
  color: blue;
  &:hover {
    text-decoration: underline;
  }
  &:visited {
    color: purple;
  }
}

//css
a.myAnchor {
  color: blue;
}
a.myAnchor:hover {
  text-decoration: underline;
}
a.myAnchor:visited {
  color: purple;
}
```
离开嵌套回到顶层（根级）选择器，那么我们可以使用 `@at-root` 指令

```scss
//scss
.first-component {
  .text { font-size: 1.4rem; }
  .button { font-size: 1.7rem; }
  @at-root .second-component {
    .text { font-size: 1.2rem; }
    .button { font-size: 1.4rem; }
  }
}

//css
.first-component .text {
  font-size: 1.4rem;
}
.first-component .button {
  font-size: 1.7rem;
}
.second-component .text {
  font-size: 1.2rem;
}
.second-component .button {
  font-size: 1.4rem;
}
```

> Inception Rule：选择器深度不要超过四层。——thesassway


## 五、继承

#### @extend

使用`@extend`指令扩展input类，指向input-error类

```scss
//scss
.input {
  border-radius: 3px;
  border: 4px solid #ddd;
  color: #555;
  font-size: 17px;
  padding: 10px 20px;
  display: inline-block;
  outline: 0;
}
.error-input {
  @extend .input;
  border:4px solid #e74c3c;
}
```

**请注意：**这么做并不会从`.input`复制样式到`.error-input`中。

编译后如下：

```css
/*css*/
.input, .error-input {
  border-radius: 3px;
  border: 4px solid #ddd;
  color: #555;
  font-size: 17px;
  padding: 10px 20px;
  display: inline-block;
  outline: 0;
}
.error-input {
  border: 4px solid #e74c3c;
}
body {
  text-align: center;
  padding-top: 100px;
}
```

#### 占位选择器`%`

从sass 3.2.0以后就可以定义占位选择器`%`。这种选择器的优势在于：如果不调用则不会有任何多余的css文件，避免了以前在一些基础的文件中预定义了很多基础的样式，然后实际应用中不管是否使用了`@extend`去继承相应的样式，都会解析出来所有的样式。占位选择器以`%`标识定义，通过`@extend`调用。

```scss
//scss
%mod-input {
  border-radius: 3px;
  color: #555;
  font-size: 17px;
  padding: 10px 20px;
  display: inline-block;
  outline: 0;
}
.input {
  @extend %mod-input;
  border: 4px solid #ddd;
}
.input-error {
  @extend %mod-input;
  border:4px solid #e74c3c;
}

//css
.input, .input-error {
  border-radius: 3px;
  color: #555;
  font-size: 17px;
  padding: 10px 20px;
  display: inline-block;
  outline: 0;
}
.input {
  border: 4px solid #ddd;
}
.input-error {
  border: 4px solid #e74c3c;
}
```


## 六、混合

sass中使用`@mixin`声明混合，可以传递参数，参数名以`$`符号开始，多个参数以逗号分开，也可以给参数设置默认值。声明的`@mixin`通过`@include`来调用。

#### 无参数mixin

```scss
//scss
@mixin center-block {
    margin-left:auto;
    margin-right:auto;
}
.demo{
    @include center-block;
}

//css
.demo{
    margin-left:auto;
    margin-right:auto;
}
```

#### 有参数mixin

```scss
//scss
@mixin opacity($opacity:50) {
  opacity: $opacity / 100;
  filter: alpha(opacity=$opacity);
}

//css
.opacity{
  @include opacity; //参数使用默认值
}
.opacity-80{
  @include opacity(80); //传递参数
}
```

#### 多个参数mixin

调用时可直接传入值，如`@include`传入参数的个数小于`@mixin`定义参数的个数，则按照顺序表示，后面不足的使用默认值，如不足的没有默认值则报错。除此之外还可以选择性的传入参数，使用参数名与值同时传入。

```scss
//scss
@mixin horizontal-line($border:1px dashed #ccc, $padding:10px){
    border-bottom:$border;
    padding-top:$padding;
    padding-bottom:$padding;  
}
.imgtext-h li{
    @include horizontal-line(1px solid #ccc);
}
.imgtext-h--product li{
    @include horizontal-line($padding:15px);
}

//css
.imgtext-h li {
    border-bottom: 1px solid #cccccc;
    padding-top: 10px;
    padding-bottom: 10px;
}
.imgtext-h--product li {
    border-bottom: 1px dashed #cccccc;
    padding-top: 15px;
    padding-bottom: 15px;
}
```

#### 多组值参数mixin

如果一个参数可以有多组值，如`box-shadow`、`transition`等，那么参数则需要在变量后加三个点表示，如`$variables...`。

```scss
//scss
//box-shadow可以有多组值，所以在变量参数后面添加...
@mixin box-shadow($shadow...) {
  -webkit-box-shadow:$shadow;
  box-shadow:$shadow;
}
.box{
  border:1px solid #ccc;
  @include box-shadow(0 2px 2px rgba(0,0,0,.3),0 3px 3px rgba(0,0,0,.3),0 4px 4px rgba(0,0,0,.3));
}

//css
.box{
  border:1px solid #ccc;
  -webkit-box-shadow:0 2px 2px rgba(0,0,0,.3),0 3px 3px rgba(0,0,0,.3),0 4px 4px rgba(0,0,0,.3);
  box-shadow:0 2px 2px rgba(0,0,0,.3),0 3px 3px rgba(0,0,0,.3),0 4px 4px rgba(0,0,0,.3);
}
```

#### @content

`@content`在sass3.2.0中引入，可以用来解决css3的`@media`等带来的问题。它可以使`@mixin`接受一整块样式，接受的样式从`@content`开始。

```scss
@mixin media($queryString){
  //...
}
```

注意我们在混合宏media中声明了一个$queryString参数。当我们引入混合宏时，可以一个字符串参数以实现动态渲染。

```scss
@mixin media($queryString){
  @media #{$queryString} {
    @content;
  }
}
```

因为我们期待字符串参数被目标函数使用，所以使用了Sass的插值语法，#{}。当你传递变量到这个括号中时，变量会像字符串一样输出而不是进行某种逻辑运算。
这个例子中另一个生疏的地方是@content指令。当你使用的混合宏后接被大括号包裹的样式，那么被包裹样式就可以通过@content指令加以使用。

```scss
//scss
@mixin media($queryString){
  @media #{$queryString} {
    @content;
  }
}
.container {
  width: 900px;
  @include media("(max-width: 767px)"){
    width: 100%;
  }
}

//css
.container {
  width: 900px;
}
@media (max-width: 767px) {
  .container {
    width: 100%;
  }
}
```

使用`@content`的好处：在需要Media Queries的地方，可以快速插入，而不需要在专门的区域重新定义。

> __PS：__ `@mixin`通过`@include`调用后解析出来的样式是以拷贝形式存在的，而下面的继承则是以联合声明的方式存在的，所以从3.2.0版本以后，建议传递参数的用`@mixin`，而非传递参数类的使用下面的继承`%`。


## 七、函数

官方列表：<a href="http://sass-lang.com/documentation/Sass/Script/Functions.html" target="_blank" title="函数列表">sass function</a>

在Sass中，函数指令类似于混合宏，sass默认已有很多函数，定义函数的方式以`@function`它们会通过`@return`指令返回值而不是返回样式。这可以降低代码中的重复率并提高可读性。

```scss
//scss
$baseFontSize:      10px !default;
$gray:              #ccc !default;        

// pixels to rems 
@function pxToRem($px) {
  @return $px / $baseFontSize * 1rem;
}

body{
  font-size:$baseFontSize;
  color:lighten($gray,10%);
}
.test{
  font-size:pxToRem(16px);
  color:darken($gray,10%);
}

//css
body {
  font-size: 10px;
  color: #e6e6e6;
}

.test {
  font-size: 1.6rem;
  color: #b3b3b3;
}

```

**<a rel="nofollow" href="http://sassmeister.com/gist/0a041d0fb2d72758c280" target="_blank" title="">演示demo</a>**

关于`@mixin`、`%`、`@function`更多可参考：

- <a rel="nofollow" href="http://www.w3cplus.com/preprocessor/sass-mixins-function-placeholder.html">sass揭秘之@mixin，%，@function</a>
- <a rel="nofollow" href="http://www.w3cplus.com/preprocessor/sass-color-function.html">Sass基础——颜色函数</a>
- <a rel="nofollow" href="http://www.w3cplus.com/preprocessor/sass-other-function.html">Sass基础——Sass函数</a>


## 八、运算

sass可以使用各种算式进行值的计算：

```scss
//scss
$num : 2;
body {
  margin: (14px/2);
  top: 50px + 100px;
  right: $num * 10%;
}

//css
body {
  margin: 7px;
  top: 150px;
  right: 20%;
}
```

#### 加法：

加法运算不仅仅是数字计算，也可以像js一样连接字符串。

```scss
//scss
p{
  text-decoration: line + -through;
}

//css
p {
  text-decoration: line-through;
}
```

加法连接字符串也有一定的规则：如果前面字符串带有引号，后面字符串会自动包含在引号中，如果前面没有，后面带有引号的字符串也会去掉引号：

```scss
//scss
p:before {
  content: "Foo " + Bar;
  font-family: sans- + "serif";
}

//css
p:before {
  content: "Foo Bar";
  font-family: sans-serif;
}
```

运算时会以空格作为分割，会操作相邻的两个数值，如：

```scss
//scss
p {
  margin: 3px + 4px auto;
}

//css
p {
  margin: 7px auto;
}
```

插值运算`#{}`在字符串中的使用：

```scss
//scss
$w  : 10;
p:before {
  content: "I ate #{5 + $w} pies!";
}

//css
p:before {
  content: "I ate 15 pies!";
}
```

#### 除法：

除法也有很多需要注意的地方，因为除法的运算符 `/` 在 CSS 中也有遇到，例如 font 缩写属性时候的 font-size 和 line-height 属性，就需要 `/` 来分割。所以，在这些包含 `/` 的 CSS 属性中对应位置的值，是不会参与运算的，除了下面情况下：

- 如果两个值其中一个或两个存放在变量中或者是由函数返回的值； // $num / 2  or  rem(40) / 2
- 如果值被包裹在一对括号里面； // (12px/2)
- 如果值被作为另一个表达式的一部分。  // 10px + 15px/2

```scss
//scss
p {
  font: 10px/8px;             // 纯 CSS 不会运算
  $width: 1000px;
  width: $width/2;            // 使用变量，执行运算
  width: round(1.5)/2;        // 使用函数返回值，执行运算
  height: (500px/2);          // 使用括号包裹，执行运算
  margin-left: 5px + 8px/2px; // 用了加法，作为表达式的一部分，执行运算
}

//css
p {
  font: 10px/8px;
  width: 500px;
  width: 1;
  height: 250px;
  margin-left: 9px;
}
```


**注意事项：**

1.运算符的两边最好保留空格。

```scss
//scss
$w : 7px;
$h : 10px;
p:before {
  width: $h-$w; // 报错：Undefined variable: "$h-".
}
```

2.运算单位不要混合使用。

```scss
$num : 10px;
div{
  margin: $num * 20%; //报错：200%*px isn't a valid CSS value.
}
```

**总的运算规则如下：**

 - 加法：都没有单位输出纯数字；一方有单位，则结果输出该单位；两方相同单位，结果输出该单位；双方单位不同，报错。
 - 减法：类似加法。
 - 除法：两方相同单位，结果无单位；都没有单位，结果无单位；一方有单位另一方无单位，报错。
 - 乘法：两方相同单位，报错；一方有单位，结果输出该单位；两方都无单位，输出无单位。


## 九、控制语句

#### @if

`@if`作为判断语句，除了判断真假，还能判断或（`or`）、非（`not`）、与（`and`）、等于（`==`）、不等于（`!=`）

```scss
//inline-block
@mixin inline-block($lte7:true) {
  display: inline-block;
  @if $lte7 { 
    *display: inline;*zoom:1;
  }
}
.div1{
  @include inline-block();
}
.div2{
  @include inline-block(false);
}

//css
.div1 {
  display: inline-block;
  *display: inline;
  *zoom: 1;
}
.div2 {
  display: inline-block;
}
```

**三目运算：**

语法为：`if($condition, $if_true, $if_false)`，三个参数分别表示：条件，条件为真的值，条件为假的值。

```scss
//scss
$fontBold: true;
.title {
  font-weight: if($fontBold, bold, normal);
}

//css
.title {
  font-weight: bold;
}
```


#### @for

语法：`@for $i from start through end` 或 `@for $i from start to end`，`$i`表示要循环的变量，`start`为起始值，`end`为结束值。<br>
唯一区别为`through`表示包含结束值，`to`则表示不包含结束值。

```scss
//scss
@for $i from 1 through 5 {
  .div#{$i}{
    background: url(images/bg#{$i}.jpg);
  }
}

//css
.div1 {
  background: url(images/bg1.jpg);
}
.div2 {
  background: url(images/bg2.jpg);
}
.div3 {
  background: url(images/bg3.jpg);
}
.div4 {
  background: url(images/bg4.jpg);
}
.div5 {
  background: url(images/bg5.jpg);
}
```

`@for`语句有两种：

- @for $i from 1 through 5，through表示包含5
- @for $i from 1 to 5，  to则反之

#### @each

语法：`@each $i in a, b, c`，具体实现看如下demo：

```scss
//scss
$icons : tag, name, cut, tel, num;
@each $i in $icons{ //或者： @each $i in tag, name, cut, tel, num{}
  .icon-#{$i}{
    background-img: url(images/icon-#{$i}.png);
  }
}

//css
.icon-tag {
  background-img: url(images/icon-tag.png);
}
.icon-name {
  background-img: url(images/icon-name.png);
}
.icon-cut {
  background-img: url(images/icon-cut.png);
}
.icon-tel {
  background-img: url(images/icon-tel.png);
}
.icon-num {
  background-img: url(images/icon-num.png);
}
```

一个经典循环 css sprite 的做法：

```scss
//scss
$sprite: puma sea-slug egret salamander !default;

%sprite-animal{
  background: url('/images/animal.png') no-repeat;
}
@each $animal in $sprite {
    .#{$animal}-icon {
        @extend %sprite-animal;     
        background-position:0 -(index($sprite,$animal)*30px);
    }
}

//css
.puma-icon, .sea-slug-icon, .egret-icon, .salamander-icon {
  background: url("/images/animal.png") no-repeat;
}
.puma-icon {
  background-position: -30px;
}
.sea-slug-icon {
  background-position: -60px;
}
.egret-icon {
  background-position: -90px;
}
.salamander-icon {
  background-position: -120px;
}
```

#### @while

`@while`和`@for`循环非常相似，不过`@while`可以控制循环步数。

```scss
//scss
$i: 6;
@while $i > 0 {
  .item-#{$i} { width: 2em * $i; }
  $i: $i - 2;
}

//css
.item-6 {
  width: 12em;
}
.item-4 {
  width: 8em;
}
.item-2 {
  width: 4em;
}
```

<br>

#### sass相关工具推荐

- <a rel="nofollow" href="http://sassmeister.com/" target="_blank">sass在线编译</a>
- <a rel="nofollow" href="http://koala-app.com/index-zh.html" target="_blank">sass可视化编译工具: Koala</a>
