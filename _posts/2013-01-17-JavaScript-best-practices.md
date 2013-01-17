---
layout: default
title: Javascript最佳实践 
---
http://www.w3.org/wiki/JavaScript_best_practices

# 一.变量命名：简短且有意义的变量名和方法名

Call things by their name — easy, short and readable variable and function names

bad:

var x1, x2;

var incrementorForMainLoopWhichSpansFromTenToTwenty;

good:

function isLegalDrinkingAge()

匈牙利命名法(Hungarian notation)，可以方便地知道变量的类型:

sFamilyName: 字符串

oMember: 对象

bIsLegal: 布尔

使用英语。

让你的代码讲故事，让别人像读故事一样读懂你的代码。

# 二.避免使用全局变量

原因显而易见，那如何避免呢？

bad:

  var current = null;

  function init(){…};

  function change(){…};

  function verify(){…};



1.定义成一个对象：

  var myNameSpace = {
  
       current: null,
  
       init: function(){…},
  
       change: function(){…},
  
       verify: function(){...}
  
  };

缺点：每一个需要调用的地方，都要带上对象的名字。比如：

  myNameSpace.init();

2.通过一个匿名方法包裹(模块模式):

  myNameSpace = function(){
  
    var current = null;
  
    function init(){…}
  
    function change(){…}
  
    function verify(){…}
  
  }();

缺点：外部不能访问。如果给外部提供调用，就要将特定的内容return出去:

3.

  myNameSpace = function(){
    var current = null;
    function verify(){…}
    return{
      init:function(){…}
      change:function(){…}
    }
  }();

4.最佳实践：

  myNameSpace = function(){
  
    var current = null;
  
    function init(){…}
  
    function change(){…}
  
    function verify(){…}
  
    return{
  
      init:init,
  
      change:change
  
    }
  
  }();

不直接返回变量或方法，而是返回指向它们的指针。这样的话，不非得通过myNameSpace来访问其中的变量和方法。

另外，可以以另外的名字(更为简短的)来返回，如return {set: change};

5.如果不想暴露任何信息到外部，直接包起来即可：

  function(){
  
    var current = null;
  
    function init(){…}
  
    function change(){…}
  
    function verify(){…}
  
    return{
  
      init:init,
  
      change:change
  
    }
  
  }();

# 三.坚持严格的编码风格

浏览器对于js很是宽容，但这不表明你就可以随意写。

JSLint

# 四.注释是必要的，但不能过度

单行注释(//)，如果只是通过删除空格空行压缩的话，会出问题。

对于多行注释的一个技巧：在注释结束符之前写两个反杠(//)，这样可以通过在注释开始符前增加或删除 //

来打开或关闭代码块：

  /*
  function init() {
  }
  // */

打开代码块：

  // /*
  function init() {
  }
  // */

# 五.避免混用其他技术

比如在js里面直接对元素的样式值(类似 color, font-size)进行修改，而是通过操作元素的class值来实现。

# 六.适当的时候使用快捷符号(shortcut notation):

  var cow = new Object();
  
  cow.colour = 'brown';
  
  cow.commonQuestion = 'What now?';
  
  cow.moo = function(){
  
    console.log('moo');
  
  }
  
  cow.feet = 4;
  
  cow.accordingToLarson = 'will take over the world';
==>
  var cow = {
  
    colour:'brown',
  
    commonQuestion:'What now?',
  
    moo:function(){
  
      console.log('moo);
  
    },
  
    feet:4,
  
    accordingToLarson:'will take over the world'
  
  };

数组：

  var aweSomeBands = new Array();
  
  aweSomeBands[0] = 'Bad Religion';
  
  aweSomeBands[1] = 'Dropkick Murphys';

==>
  var aweSomeBands = [
    'Bad Religion',
    'Dropkick Murphys'
  ];

条件判断使用三目运算符：

  var a = (x ? 100) ? 1 : -1;

赋值：

  var x = v || 10;

当v不存在值，把x设为 10。

# 七.模块化，一个方法只完成一件事

保持函数功能的单一也方便其他人维护。

对于方法，要有输入，有输出，而不是把功能都方在方法内部。

# 八.Enhance progressively，逐步提高

在浏览器禁用javascript的情况下，保证主要功能可用。

# 九.提供可变配置



# 十.避免过深的嵌套



# 十一.优化循环

bad:

  for(var i = 0; i < arr.length; i++) {…}

good:

  for(var i = 0, iMax = arr.length; i < iMax; i++) {…}



# 十二.尽量少地操作DOM元素



# 十三.提高代码的兼容性

不要只针对某个浏览器或某个版本进编码，尽量使编码标准代码。如果必须这样做，将文件单独放起来，

并以浏览器名称及版本号命名。



# 十四.不要相信任何数据

对于数据，在处理之前要多加验证。



# 十五.用javascript来增加功能，而不是过多得创建内容

bad:

在javascript代码中掺杂了大量的html

good:

加载模板创建html内容

# 十六.站在巨人的臂膀上

选择一个适合自己项目的js库。尽量不要同时使用多个库
