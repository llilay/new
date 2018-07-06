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
