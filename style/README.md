# scss

## 1. SASS和SCSS的区别
        文件扩展名不同：“.sass”和“.scss”;
        SASS是以严格缩进式语法规则来书写的，不带大括号和分号；而SCSS的语法和CSS书写语法类似。
## 2. 安装
        先安装Ruby(http://www.ruby-lang.org/zh_cn/downloads/)
        通过命令安装sass: 打开命令终端，输入：gem install sass

## 3. sass编译

### 3.1 单文件编译
        sass <编译的sass文件路径>/xx.scss: <输入的css路径>/xx.css\<br>
        sass input.scss:output.css

### 3.2 多文件编译
        sass src/sass/:dist/css/
上面的命令表示将项目中“sass”文件夹中所有“.scss”(“.sass”)文件编译成“.css”文件，并且将这些 CSS 文件都放在项目中“css”文件夹中。

#### 3.3 SASS提供四个编译风格的选项：
* nested：嵌套缩进的css代码，它是默认值。
* expanded：没有缩进的、扩展的css代码。
* compact：简洁格式的css代码。
* compressed：压缩后的css代码。
在编译的时候带上参数' --style expanded':

在生产环境中，一般使用最后一个

* sass --style compressed test.sass test.css
### 3.4 watch监控sass文件或目录
  // watch a file
        sass --watch input.scss:output.css
　// watch a directory
        sass --watch app/sass:public/css
官方在线工具 https://www.sassmeister.com/

## 用法
### 1. 变量
sass允许使用变量，所有变量以$开头。

普通变量声明后在全局范围内可使用
默认变量在值的后面加上!default即可

$side: left;
如果变量需要嵌套在字符串之中，就必须需要写在#{}之中:

$side: left;
.rouned {
    border-#{$side}-radius: 3px;
}
### 2. 加减乘除
sass允许在代码中使用算式：

body {

    margin:(14px/2);
    top: 50px + 100px;   // 变量或属性均可以
    right: $var * 10%;
    left: 10px * 2px; // 报错
    left: 10px * 2; // 正确
}
注意，sass除法较为特殊.如果除式中没有变量或者不是在一个数学表达式中（有加法减法等），就要将除式运算用小括号括起来，否则“/”不会被当做除号运算。

p {
  font: 10px/8px; // 纯CSS，不是除法运算
  width: $width/2;  // 使用了变量，是除法运算
  width: round(1.5)/2;  // 使用了函数，是除法运算
  height: (500px/2); // 使用了圆括号，是除法运算
  margin-left: 5px + 8px/2px; // 使用了加（+）号，是除法运算
}
### 3. 嵌套
* 3.1 选择器嵌套
div h1 {
    color: red;
}



// 可以写成如下：
div {
    h1 {
        color: red;
    }
}
在嵌套的代码块中，可以使用&引用父元素

a {
    &:hover {
        color: red;
    }
}
* 3.2 属性嵌套
如border-color、font系列等

p {
    border: {
        color: red;
    }

    font: {
        size: 12px;
        weight: 700;
    }
}
注意，border必须加上冒号：

### 4. 注释
SASS共有两种注释风格。

标准的CSS注释:

/* comment */ ，会保留到编译后的文件。

单行注释:

// comment，只保留在SASS源文件中，编译后被省略。

在/*后面加一个感叹号，表示这是"重要注释"。即使是压缩模式编译，也会保留这行注释，通常可以用于声明版权信息。

/*!
    重要声明
*/
### 5. 继承 @extend
SASS允许一个选择器，继承另一个选择器，使用@extend命令。比如，现有class1：

.class1 {
    border: 1px solid #ddd;
}

.class2 {
　　@extend .class1;
　　font-size:120%;
　　}
注意：要继承的不仅仅是类选择器，可以是id选择器也可以是标签选择器，也可以是某个状态，任何选择器都能继承。

此外，可以用占位符%构建只用来继承的选择器。
用占位符%声明的代码，如果不被@extend调用就不会被编译，也不会渲染到CSS的规则集中。

%br5 {
    border-radius: 5px;
}
// 使用@extend调用：
.btn {
    @extend %bt5;
}

// 编译后
.btn {
    border-radius: 5px;
}
### 6. Mixin混合宏
Mixin有点像C语言的宏（macro），是可以重用的代码块。

首先，使用@mixin命令，定义一个代码块。

@mixin left {
　　float: left;
　　margin-left: 10px;
}
其次，使用@include调用

div {
　　@include left;
}
mixin的强大之处，在于可以指定参数和缺省值。

@mixin left($value: 10px) {
　　float: left;
　　margin-right: $value;
　　}
### 7. 插入/引入外部文件
@import命令，用来插入外部文件。

@import "path/filename.scss";
### 8. 条件语句
* @if语句

@if lightness($color) > 30% {
　　　　background-color: #000;
　　} @else {
　　　　background-color: #fff;
　　}
### 9. 循环
* @for循环

        @for $i from 1 to 10 {
        　　.border-#{$i} {
        　　    border: #{$i}px solid blue;
        　　}
        }
* @while循环

        $i: 6;
        @while $i > 0 {
        　　.item-#{$i} { width: 2em * $i; }
        　　$i: $i - 2;
        }
* @each命令

        @each $member in a, b, c, d {
        　　.#{$member} {
        　　　　background-image: url("/image/#{$member}.jpg");
        　　}
        }
### 10. 自定义函数
           @function double($n) {
        　　　　@return $n * 2;
        　　}
        　　#sidebar {
        　　　　width: double(5px);
        　　}

