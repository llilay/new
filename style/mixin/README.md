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