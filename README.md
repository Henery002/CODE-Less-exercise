## Less 学习笔记
> 2017/12/17  
> 参考文章：IBM developerWorks - [less css框架简介](https://www.ibm.com/developerworks/cn/web/1207_zhaoch_lesscss/)

### 简介
css的语法相对简单，对使用者的要求较低，但同时也带来一些问题：css需要书写大量看似没有逻辑的代码，不方便维护及扩展，不利于复用，很难写出组织良好且易于维护的css代码，因为css是一门非程序式语言，没有变量、函数、scope（作用域）等概念。
less在css的语法基础之上，引入了变量，Mixin（混入），运算以及函数等功能，大大简化了css的编写，并且降低了css的维护成本。

### less原理及使用方式
本质上，less 包含一套自定义的语法及一个解析器，用户根据这些语法定义自己的样式规则，这些规则最终会通过解析器，编译生成对应的 css 文件。less 并没有裁剪 css 原有的特性，更不是用来取代 css 的，而是在现有 css 语法的基础上，为 css 加入程序式语言的特性。下面是一个简单的例子：
``` less
@color: #4D926F;  
#header { 
    color: @color; 
} 
h2 { 
    color: @color; 
}
```
经过编译生成的 css 文件如下：
``` css
#header { 
    color: #4D926F; 
} 
h2 { 
    color: #4D926F; 
}
```
从上面的例子可以看出，学习 less 非常容易，只要你了解 css 基础就可以很容易上手。
less 可以直接在客户端使用，也可以在服务器端使用。在实际项目开发中，我们更推荐使用第三种方式，将 less 文件编译生成静态 css 文件，并在 html 文档中应用。
* #### 客户端
我们可以直接在客户端使用 .less（less 源文件），只需要从 http://lesscss.org下载 less.js 文件，然后在我们需要引入 less 源文件的 html 中加入如下代码：
``` html 
<link rel="stylesheet/less" type="text/css" href="styles.less">
```
less 源文件的引入方式与标准 css 文件引入方式一样。
需要注意的是：在引入 .less 文件时，rel 属性要设置为“stylesheet/less”。还有更重要的一点需要注意的是：less 源文件一定要在 less.js 引入之前引入，这样才能保证 less 源文件正确编译解析。
* #### 服务器端
less 在服务器端的使用主要是借助于 less 的编译器，将 less 源文件编译生成最终的 css 文件，目前常用的方式是利用 node 的包管理器 (npm) 安装 less，安装成功后就可以在 node 环境中对 less 源文件进行编译。
在项目开发初期，我们无论采用客户端还是服务器端的用法，我们都需要想办法将我们要用到的 css 或 less 文件引入到我们的 html 页面或是桥接文件中，less 提供了一个我们很熟悉的功能— Importing。我们可以通过这个关键字引入我们需要的 .less 或 .css 文件。 如：
``` js
@import “variables.less”;
```
.less 文件也可以省略后缀名，像这样：
``` js
@import “variables”;
```
引入 css 同 less 文件一样，只是 .css 后缀名不能省略。
* #### 使用编译生成的静态css文件
我们可以通过 less 的编译器，将 less 文件编译成为 css 文件，在 html 文章中引入使用。这里要强调的一点，less 是完全兼容 css 语法的，也就是说，我们可以将标准的 css 文件直接改成 .less 格式，less 编译器可以完全识别。

### 语法
1. #### 变量
less 允许开发者自定义变量，变量可以在全局样式中使用，变量使得样式修改起来更加简单。
``` less
@border-color : #b5bcc7; 
.mythemes tableBorder{ 
    border : 1px solid @border-color; 
}
```
经过编译生成的 css 文件如下：
``` css
.mythemes tableBorder { 
    border: 1px solid #b5bcc7; 
}
```
从上面的代码中我们可以看出，变量是 value（值）级别的复用，可以将相同的值定义成变量统一管理起来。
该特性适用于定义主题，我们可以将背景颜色、字体颜色、边框属性等常规样式进行统一定义，这样不同的主题只需要定义不同的变量文件就可以了。当然该特性也同样适用于 CSS RESET（重置样式表），在 web 开发中，我们往往需要屏蔽浏览器默认的样式行为而需要重新定义样式表来覆盖浏览器的默认行为，这里可以使用 less 的变量特性，这样就可以在不同的项目间重用样式表，我们仅需要在不同的项目样式表中，根据需求重新给变量赋值即可。
less 中的变量和其他编程语言一样，可以实现值的复用，同样它也有生命周期，也就是 scope（变量范围，开发人员惯称之为作用域），简单的讲就是局部变量还是全局变量的概念，查找变量的顺序是先在局部定义中找，如果找不到，则查找上级定义，直至全局。下面我们通过一个简单的例子来解释 scope。
``` less
@width : 20px; 
#homeDiv { 
    @width : 30px; 
    #centerDiv{ 
        width : @width;// 此处应该取最近定义的变量 width 的值 30px 
    } 
} 
#leftDiv { 
    width : @width; // 此处应该取最上面定义的变量 width 的值 20px 
}
```
经过编译生成的 css 文件如下：
``` css
#homeDiv #centerDiv { 
    width: 30px; 
} 
#leftDiv { 
    width: 20px; 
}
```
2. #### Mixins(混入)
Mixins（混入）功能对用开发者来说并不陌生，很多动态语言都支持 Mixins（混入）特性，它是多重继承的一种实现，在 less 中，混入是指在一个 class 中引入另外一个已经定义的 class，就像在当前 class 中增加一个属性一样。
我们先简单看一下 Mixins 在 less 中的使用：
``` less
// 定义一个样式选择器
 .roundedCorners(@radius:5px) { 
     -moz-border-radius: @radius; 
     -webkit-border-radius: @radius; 
     border-radius: @radius; 
 } 
 // 在另外的样式选择器中使用
 #header { 
    .roundedCorners; 
 } 
 #footer { 
    .roundedCorners(10px); 
 }
```
经过编译生成的 css 文件如下：
``` css
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
从上面的代码我们可以看出：Mixins 其实是一种嵌套，它允许将一个类嵌入到另外一个类中使用，被嵌入的类也可以称作变量，简单的讲，Mixins 其实是规则级别的复用。
Mixins 还有一种形式叫做 Parametric Mixins（混入参数），less 也支持这一特性：
``` less
// 定义一个样式选择器
 .borderRadius(@radius){ 
     -moz-border-radius: @radius; 
     -webkit-border-radius: @radius; 
     border-radius: @radius; 
 } 
 // 使用已定义的样式选择器
 #header { 
    .borderRadius(10px); // 把 10px 作为参数传递给样式选择器
 } 
 .btn { 
    .borderRadius(3px);// // 把 3px 作为参数传递给样式选择器
 }
```
经过编译生成的 css 文件如下：
``` css
#header { 
    -moz-border-radius: 10px; 
    -webkit-border-radius: 10px; 
    border-radius: 10px; 
} 
.btn { 
    -moz-border-radius: 3px; 
    -webkit-border-radius: 3px; 
    border-radius: 3px; 
}
```
我们还可以给 Mixins 的参数定义一个默认值，如
``` less
.borderRadius(@radius:5px){ 
     -moz-border-radius: @radius; 
     -webkit-border-radius: @radius; 
     border-radius: @radius; 
 } 
 .btn { 
    .borderRadius; 
 }
```
经过编译生成的 css 文件如下：
``` css
.btn { 
    -moz-border-radius: 5px; 
    -webkit-border-radius: 5px; 
    border-radius: 5px; 
}
```
像 js 中 arguments一样，Mixins 也有这样一个变量：@arguments。@arguments 在 Mixins 中是一个很特别的参数，当 Mixins 引用这个参数时，该参数表示所有的变量，很多情况下，这个参数可以省去你很多代码。
``` less
.boxShadow(@x:0,@y:0,@blur:1px,@color:#000){ 
    -moz-box-shadow: @arguments; 
    -webkit-box-shadow: @arguments; 
    box-shadow: @arguments; 
} 
#header { 
    .boxShadow(2px,2px,3px,#f36); 
}
```
经过编译生成的 css 文件如下：
``` css
#header { 
    -moz-box-shadow: 2px 2px 3px #FF36; 
    -webkit-box-shadow: 2px 2px 3px #FF36; 
    box-shadow: 2px 2px 3px #FF36; 
}
```
Mixins 是 less 中很重要的特性之一，我们这里也写了很多例子，看到这些例子你是否会有这样的疑问：当我们拥有了大量选择器的时候，特别是团队协同开发时，如何保证选择器之间重名问题？如果你是 java 程序员或 C++ 程序员，我猜你肯定会想到命名空间 Namespaces，less 也采用了命名空间的方法来避免重名问题，于是乎 less 在 mixins 的基础上扩展了一下，看下面这样一段代码：
``` less
#mynamespace { 
    .home {...} 
    .user {...} 
}
```
这样我们就定义了一个名为 mynamespace 的命名空间，如果我们要复用 user 这个选择器的时候，我们只需要在需要混入这个选择器的地方这样使用就可以了。
```
#mynamespace > .user。
```
3. #### 嵌套的规则
在我们书写标准 css 的时候，遇到多层的元素嵌套这种情况时，我们要么采用从外到内的选择器嵌套定义，要么采用给特定元素加 class 或 id 的方式。在 less 中我们可以这样写：
``` html
<div id="home"> 
    <div id="top">top</div> 
    <div id="center"> 
        <div id="left">left</div> 
        <div id="right">right</div> 
    </div> 
</div>
```
``` less
#home{ 
  color : blue; 
  width : 600px; 
  height : 500px; 
  border:outset; 
  #top{ 
       border:outset; 
       width : 90%; 
  } 
  #center{ 
       border:outset; 
       height : 300px; 
       width : 90%; 
       #left{ 
            border:outset; 
            float : left; 
            width : 40%; 
       } 
       #right{ 
            border:outset; 
            float : left; 
            width : 40%; 
       } 
   } 
}
```
经过编译生成的 css 文件如下：
``` css
#home { 
    color: blue; 
    width: 600px; 
    height: 500px; 
    border: outset; 
} 
#home #top { 
    border: outset; 
    width: 90%; 
} 
#home #center { 
    border: outset; 
    height: 300px; 
    width: 90%; 
} 
#home #center #left { 
    border: outset; 
    float: left; 
    width: 40%; 
} 
#home #center #right { 
    border: outset; 
    float: left; 
    width: 40%; 
}
```
从上面的代码中我们可以看出，less 的嵌套规则的写法是 html 中的 DOM 结构相对应的，这样使我们的样式表书写更加简洁和更好的可读性。同时，嵌套规则使得对伪元素的操作更为方便。
``` less
a { 
    color: red; 
    text-decoration: none; 
    &:hover {                           // 有 & 时解析的是同一个元素或此元素的伪类，没有 & 解析是后代元素
        color: black; 
        text-decoration: underline; 
    } 
}
```
经过编译生成的 css 文件如下：
``` css
a { 
    color: red; 
    text-decoration: none; 
} 
a:hover { 
    color: black; 
    text-decoration: underline; 
}
```
4. #### 运算及函数
在我们的 css 中充斥着大量的数值型的 value，比如 color、padding、margin 等，这些数值之间在某些情况下是有着一定关系的，那么我们怎样利用 less 来组织我们这些数值之间的关系呢？我们来看这段代码：
``` less
@init: #111111; 
@transition: @init*2; 
.switchColor { 
    color: @transition; 
}
```
经过编译生成的 css 文件如下：
``` css
.switchColor { 
 color: #222222; 
}
```
上面的例子中使用 less 的 operation 是 特性，其实简单的讲，就是对数值型的 value（数字、颜色、变量等）进行加减乘除四则运算。同时 less 还有一个专门针对 color 的操作提供一组函数。下面是 less 提供的针对颜色操作的函数列表：
``` less
lighten(@color, 10%);           // return a color which is 10% *lighter* than @color 
darken(@color, 10%);            // return a color which is 10% *darker* than @color 
saturate(@color, 10%);          // return a color 10% *more* saturated than @color 
desaturate(@color, 10%);        // return a color 10% *less* saturated than @color 
fadein(@color, 10%);            // return a color 10% *less* transparent than @color 
fadeout(@color, 10%);           // return a color 10% *more* transparent than @color 
spin(@color, 10);               // return a color with a 10 degree larger in hue than @color 
spin(@color, -10);              // return a color with a 10 degree smaller hue than @color
```
PS: 上述代码引自 less css 官方网站，详情请见 [http://lesscss.org/#-color-functions]( http://lesscss.org/#-color-functions)
使用这些函数和 js 中使用函数一样。
``` less
init: #f04615; 
#body { 
    background-color: fadein(@init, 10%); 
}
```
经过编译生成的 css 文件如下：
``` css
#body { 
    background-color: #f04615; 
}
```
从上面的例子我们可以发现，这组函数像极了 js 中的函数，它可以被调用和传递参数。这些函数的主要作用是提供颜色变换的功能，先把颜色转换成 HSL 色，然后在此基础上进行操作，less 还提供了获取颜色值的方法，在这里就不举例说明了。
less 提供的运算及函数特性适用于实现页面组件特性，比如组件切换时的渐入渐出。

5. #### Comments(注释)
适当的注释是保证代码可读性的必要手段，less 对注释也提供了支持，主要有两种方式：单行注释和多行注释，这与 js 中的注释方法一样，我们这里不做详细的说明，只强调一点：less 中单行注释 (// 单行注释 ) 是不能显示在编译后的 css 中，所以如果你的注释是针对样式说明的请使用多行注释。

6. #### less VS Sass
同类框架还有 SASS : [http://sass-lang.com/](http://sass-lang.com/), 与 less 相比，两者都属于 css 预处理器，功能上大同小异，都是使用类似程序式语言的方式书写 css, 都具有变量、混入、嵌套、继承等特性，最终目的都是方便 css 的书写及维护。
less 和 SASS 互相促进互相影响，相比之下 less 更接近 css 语法，更多两者之间的比较，请参考这篇帖子：[https://gist.github.com/674726](https://gist.github.com/674726)。
### 结束语
本文提到的只是 less 的基本功能，更高级的功能如：字符串插值，服务器端使用配置，js 表达式，避免编译等可以参看 less 的官方网站。
less 以 css 语法为基础，又借用了很多我们熟知编程式语言的特性，这对于我们开发人员来讲学习成本几乎可以忽略，它在保留 css 语法的基础上扩展了更多实用的功能，less 为我们提供了一种新的编写样式表的方法，我们可以根据我们的项目特性选择使用 less 的部分特性，我们只需用很少的成本就可以换了很大的回报，一句话，less is more，借助 less 可以更便捷的进行 Web 开发。
### 相关主题
* 参考 [less 官方网站](http://lesscss.org/)，了解什么是 less。
* 参考 [W3SCHOOLS css](http://www.w3schools.com/css/)，了解 css。
* 参考 [SASS 官方网站](http://sass-lang.com/)，了解什么是 SASS。
* 参考 [Sass/less Comparison](https://gist.github.com/674726)，了解 SASS 和 less 异同。


