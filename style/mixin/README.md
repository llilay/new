# @mixin、 %、@function

## @mixin和%

* @mixin可以传递参数，而%不行；
* @mixin的调用方式是@include，而%的调用方式是@extend；
* @include产生的样式是以复制拷贝的方式存在的，而@extend产生的样式是以组合申明的方式存在的。

## 变量加...
一个属性可以有多个属性值
@include box-shadow(0 0 5px rgba(0,0,0,.3),inset 0 0 3px rgba(255,255,255,.5));
        @mixin box-shadow($shadow...){
            -webkit-box-shadow:$shadow;
            -moz-box-shadow:$shadow;
            box-shadow:$shadow;
        }
## @content
应用在@mixin里面
按常规来说，我们所有的样式都是在@mixin里面定义好的，然后使用的时候@include就拷贝了这段样式，但是@content改变了这一惯例，它其实没有定义样式，它是定义好了选择器，然后@include的时候，就是选择器定了，你写的样式都放在这个选择器里面。
        @mixin header{
            #header{
                @content;
            }
        }
我们来简单调用下：

        @include header{
            width:1000px;
            height:200px;
            .logo{
                width:200px;
            }
        }
解析后的css：

        #header {
            width: 1000px;
            height: 200px;
        }
        #header .logo {
            width: 200px;
        }

@content的使用价值其实体现在css3的media-queries，animation的keyframes定义，还有为浏览器兼容的定义

# %

%在这里不是做单位用的，而是作为占位选择器来用的。
简单来说，就是先定义好一段样式，但是这个样式默认是不会解析出来的，等到你通过@extend调用了才会解析在css文件中，避免了生成冗余浪费的css代码。

接上面的那个@mixin center-block，因为它没有参数，而我们又想要组合申明，那么该%登场了：

        %center-block {
            @include center-block;
        }
下面我们再来给#header,.gallery调用下：

        #header{
            width:1000px;
            @extend %center-block;
        }
        .gallery{
            width:600px;
            @extend %center-block;
        }
解析成的css：

        #header, .gallery {
            margin-left: auto;
            margin-right: auto;
        }
        #header {
            width: 1000px;
        }
        .gallery {
            width: 600px;
        }

        $lte7:true;
        %clearfix {
            @if $lte7 {
                *zoom: 1;
            }
            &:before, &:after {
                content: "";
                display: table;
                font: 0/0 a;
            }
            &:after {
                clear: both;
            }
        }
clearfix大家熟悉吧，以前每次使用都要在我们的html结构上加个class.clearfix来调用，现在好了，如果哪个要调用这个，直接@extend：
        .wrap{
            @extend %clearfix;
        }
        .row{
            @extend %clearfix;
        }

# @function
@function与@mixin，%这两者的第一点不同在于sass本身就有一些内置的函数，方便我们调用，如强大的color函数；其次就是它返回的是一个值，而不是一段css样式代码什么的。

下面我们介绍几个常用的内置函数，按照官网上面地址上的顺序来。

## rgba
        分为两种：rgba($red, $green, $blue, $alpha)和rgba($color, $alpha)。

第一种跟css3一样，不介绍，第二种对我们有点用，实例：

        rgba(#102030, 0.5) => rgba(16, 32, 48, 0.5)
        rgba(blue, 0.2)    => rgba(0, 0, 255, 0.2)
## ie-hex-str
        ie-hex-str($color)

这个函数将一个颜色格式化成ie滤镜使用，在做css3使用滤镜兼容的时候用得上，实例：

        `ie-hex-str(#abc) => #FFAABBCC
        ie-hex-str(#3322BB) => #FF3322BB
        ie-hex-str(rgba(0, 255, 0, 0.5)) => #8000FF00
## darken
        darken($color,$amount)

第一个参数是颜色，第二参数是百分数介于0%-100%，表示将某个颜色变暗多少个百分比。

        darken(hsl(25, 100%, 80%), 30%) => hsl(25, 100%, 50%)
        darken(#800, 20%) => #200
## lighten
        lighten($color,$amount)

第一个参数是颜色，第二参数是百分数介于0%-100%，表示将某个颜色变亮多少个百分比。

        lighten(hsl(0, 0%, 0%), 30%) => hsl(0, 0, 30)
        lighten(#800, 20%) => #e00
## percentage
        percentage($value)

将一个没有单位的数字转成百分比形式

        percentage(0.2) => 20%
        percentage(100px / 50px) => 200%
## length
        length($list)

返回一个列表的长度

        length(10px) => 1
        length(#514721 #FFF6BF #FFD324) => 3
## nth
        nth($list, $n);

返回列表里面第n个位置的值

        nth(10px 20px 30px, 1) => 10px
        nth((Helvetica, Arial, sans-serif), 3) => sans-serif
## unit
        unit($number)

得到这个数的单位

        unit(100) => ""
        unit(100px) => "px"
        unit(3em) => "em"
        unit(10px * 5em) => "em*px"
        unit(10px * 5em / 30cm / 1rem) => "em*px/cm*rem"
## unitless
        unitless($number)

返回这个数是否没有单位

        unitless(100) => true
        unitless(100px) => false
## 三目判断
        if($condition, $if-true, $if-false)

第一个表示条件，第二个表示条件为真的值，第三个表示为假的值

        if(true, 1px, 2px) => 1px
        if(false, 1px, 2px) => 2px
可能上面的都提不起你的兴趣，一时记不住也没有关系，大概有个印象，用的时候知道sass可以提供这个函数功能，或者不清楚再去查下它的官方函数。再次提醒刚开始sass的话，注意函数是返回一个值，不能直接放到sass里面直接去运行的，会报错。你可以把这些用在判断或者属性值里面，那么就是一级棒。

## 下面我们来搞点自己定义的函数吧，先来个简单的：

// px转em
        @function pxToEm($px, $base: 16) {
            @return ($px / $base) * 1em;
        }
调用下：

        p{
            font-size:pxToEm(20);
        }
解析后的css：

        p {
            font-size: 1.25em;
        }
估计这点小罗罗，是上不了大场面的，@mixin都有一个那么神来之笔，@function怎能没有呢，下面介绍网格布局的一个计算宽度的神来之笔，来自blankwork。

//960网格布局
$_columns: 12 !default;      // 总列数
$_column-width: 60px !default;   // 单列宽
$_gutter: 20px !default;     // 间隔

@function get_width($columns:$_columns, $onlyInnerWidth: true, $_subtract:0){
  // 默认返回值
  $return: ($_column-width + $_gutter) * $columns - $_subtract !default;

  @if $onlyInnerWidth == true{
    //如果$onlyInnerWidth为true，那么减掉一个间隔$_gutter
    $return: ($_column-width + $_gutter) * $columns - $_gutter - $_subtract;
  }

  @return $return;
}
首先，你得对960的网格系统比较熟悉，不然你可能有点迷惑。如果你不了解960网格系统，建议你先在w3cplus里面找找。当然也许你还没有感受它的神来之笔，别急，先来调用下：

#container{
    width:get_width(12,false);//960px
}
.col-four{
    @extend %clearfix;
    .col{
        @include float;
        margin:0 $_gutter / 2;
        width:get_width(3); //220px

        h2{
            padding-left:10px
            width:get_width(3,true,10px); //210px
        }
    }
}
看到没，向那些.span1,.span2,..., .span12说88，想要几个格子就传递数字几，当然还有些特别需求，要不了刚好的网格，比喻比4个格子要少30px，看到上面的get_width的第三个参数不，专门负责搞定这些额外需求的，get_width(4,true,30px)得到的就是270px。

所以说这个计算宽度有三种调用形式：第一种默认的如get_width(3)得到220px；第二种不需要左右margin的如get_width(3，false)得到240px；第三种可以灵活缩小点宽度如get_width(3,true,10px)得到210px。

当然计算宽度只是第一步，还有设置column的浮动，然后wrap的闭合子元素的浮动等，都是基于这个函数，一个网格系统就在这个基础上构建成功，它的这个功能应该可以和css3的那个prefixer的@mixin媲美吧。

最后说一下，其实到最后所有的的@mixin，%，@function都是为解析后的css样式服务的，如果你不能带来以下这些优势那么可以考虑不要定义这个东西：

化繁为简；
样式可以组合申明；
浏览器兼容样式判断；
灵活实现一些基础常用功能的属性值的改变，如颜色，大小等；
sass揭秘的第二篇文章就到此为止。如果你对sass比较感兴趣但是还不会，可以试试我们的sassGuide教程，如果已经开始使用sass了，欢迎试用sassCore这个库。

顺便说下，本人的面向熟悉sass人员开发的tobe即将上线，欢迎关注，也欢迎到时拍砖。