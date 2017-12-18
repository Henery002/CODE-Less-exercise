## Less 学习笔记
> 2017/12/17  
> 参考文章：IBM developerWorks - [LESS CESS框架简介](https://www.ibm.com/developerworks/cn/web/1207_zhaoch_lesscss/)

### 简介
##### css的语法相对简单，对使用者的要求较低，但同时也带来一些问题：css需要书写大量看似没有逻辑的代码，不方便维护及扩展，不利于复用，尤其对于非前端开发工程师来讲，往往会因为缺少css编写经验而很难写出组织良好且易于维护的css代码，造成这些困难的很大原因源于css是一门非程序式语言，没有变量、函数、scope（作用域）等概念。
##### Less在css的语法基础之上，引入了变量，Mixin（混入），运算以及函数等功能，大大简化了css的编写，并且降低了css的维护成本。

### LESS原理及使用方式
* #### 客户端
* #### 服务器端
* #### 使用编译生成的静态css文件


### 语法
* #### 变量

```
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


* #### Mixins(混入)

* #### 嵌套的规则

* #### 运算及函数

* #### Comments(注释)

* #### Less VS Sass


### 结束语

### 相关主题







