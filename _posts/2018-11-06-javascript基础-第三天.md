---
layout:     post
title:      javascript基础第三天
subtitle:   js基础
date:       2018-11-06
author:     YD
header-img: img/post-bg-ios9-web.jpg
catalog: true
tags:
    - javascript
    - 基础


---

> 自做的学习笔记

##### 函数的练习

**三元运算符的实用与巧用**

```javascript
  //比较2个数的最大值
    function maxNumber(num1, num2) {
        return num1 > num2 ? num1 : num2;
    }
    console.log(maxNumber(20, 30))
   // 比较3个数中的最大值
    function triMaxNumber(x, y, z) {
        return x > y ? (x > z ? x : z) : (y > z ? y : z);
    }
    console.log(triMaxNumber(10,30,60));
```

##### 业的函数参数注释

```javascript
/**
     * 专业的函数注释,打个/并且打2个** 回车就可以了.
     * @param num1
     * @param num2
     * @returns {*}
     */
```

**函数实现斐波那契数列**

**函数综合实例**

```javascript
 /**
     * 功能 判断一个年份是否为闰年
     * @param year
     */
    function isLeapMonth(year) {
        if(year%4==0&&year%100!=0||year%400==0){
            return true;
        }
    }
    /**
     * 输入年月日 查询该日期是一年中的多少天
     * @param year
     * @param month
     * @param days
     * @returns {*}
     */
    function daysOfYear(year,month,days) {
        if(month==1){
            return days;
        }
        var months=[31,28,31,30,31,30,31,31,30,31,30,31];
        for(var i=0;i<month-1;i++){
            days+=months[i];
        }
        if(isLeapMonth(year)&&month>2){
            days++;
        }
        return days;
    }
 console.log(daysOfYear(2000,3,2));
```

##### arguments对象伪数组

不知道用户传入了多少个参数的情况下，怎么去获取用户是否传入了参数，或者传入了哪些参数。

`arguments`获取所有函数传入的值，并放在一个伪数组中。可以当数组一样使用。

##### 函数其他的定义方式

命名函数:函数如果有名字，就是命名函数。

匿名函数：函数如果没有名字，就是匿名函数。

##### 匿名函数的声明与调用

==方式一==

```javascript
//匿名函数的声明与调用
var f=function () {
    console.log("ABC");
};//注意的定义，同于变量的声明，一定要加分号
f();
```

==方式二==

```javascript
//声明的过程中直接调用
(function () {console.log("123");})();
```

注意点：

命名函数在出现同名函数的情况下，后面的函数会覆盖前面的函数，匿名函数不存在这样的情况

匿名函数在声明的时候如果使用变量去接收的话，注意结尾一定要使用`;` 去结尾。

##### 函数也是一种数据类型

函数的数据类型为`function` 使用方法typeof 对象的方式获取类型。

##### 函数可以作为参数使用-回调函数

函数可以作为参数传入另一个函数中，这种情况就是回调函数。

只要看到函数作为参数使用，那么可以说这个函数就是回调函数

```javascript
 function f1(fn) {//回调函数
        fn();
    }
    function f2() {
        console.log("212");
    }
    f1(f2);
```

##### 函数也是可以被当做返回值的

**块级作用域** 大括号包裹的代码被称为块级作用域。如 for循环、if语句等。

==块作用域中的数据可以直接被外部直接调用==

**局部变量** 但是函数内部定义的变量称为局部变量。外部不能直接访问。==全局变量如果不关闭页面的情况下，一直占用内存空间。==

**全局变量** 不在局部与块作用域定义的变量称为全局变量。

**隐式全局变量** 声明的变量没有var关键字的，直接命名并赋值，那么它有什么特点吗？

==隐式全局变量不受局部变量作用域的限制，直接可被外部调用。==

==隐式全局变量可以被delete关键字直接删除，而var声明的全局变量不会一下被删除掉==

##### 作用域链

![](http://model123.oss-cn-shanghai.aliyuncs.com/18-11-9/63874006.jpg)

当函数被调用的时候，会从最下面的作用域去寻找该变量，找到则输出，找不到则向上层作用域寻找，直到找到0级作用域，如果0级作用域还没声明该变量，则报错，这就是作用域链。

##### 预解析 

提前解析代码被称为预解析

具体做了什么事情?==把变量的声明与函数的声明进行提前。==

```javascript
// 变量会被提前声明
console.log(sum);//undefined
var sum=20;
// 情况
var sum=20;
    function f1() {
        console.log(sum);//undefined--受块级链的影响
        var sum=20;
}
```

预解析只能提前到当前的作用域中，不能提前到作用域的外部。

预解析会分段（多对的script标签中定义的同名函数，预解析的不会冲突，自己玩自己的）

面试经常被问到。

```javascript
var a =25;
function abc() {
    alert(a);//undefined
    var a=10;
}
abc();
console.log(a);//25 全局作用域
/////////////////////////////////////////
//var a;预解析
function a() {
    console.log('aaaaaa');
}
    //函数与变量同名，会输出排在后面的函数名
    console.log(a);//函数
    var a=1;
    console.log(a);//1
//////////////////////////////////////
f1();
console.log(c);//9
console.log(b);//9
console.log(a);//局部变量 报错！
function f1() {
  var a = b = c = 9;//这里的理解是关键
    //var a;
    //a=b=c=9;b与c就是隐式全局变量，不受作用域的限制，函数被提前声明的情况下
  console.log(a);
  console.log(b);
  console.log(c);
}
/////////////////////////////////////////////
//匿名函数需要注意的地方
//var f1;
f1();//报错
var f1=function () {
    console.log(a);//未定义
    var a=10;
}
```





