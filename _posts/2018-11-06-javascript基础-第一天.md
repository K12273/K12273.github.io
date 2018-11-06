---
layout:     post
title:      javascript基础第一天
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

##### 什么是脚本语言？不需要编译，直接运行的语言，js就是。

##### 与之相对的是解释型的语言，遇到一行解释一行，需要经过编译。

##### js分为3个部分:

ECMAscript标注呢，基本的js语法

DOM

BOM

##### MDN是js的官方文档。

一元运算符的操作知识点

##### num++ 与 ++num的区别

**这是在一元操作符与别的运算符号组合的时候要考虑这样的情况，只有一元操作符的时候前++与后++是没有区别的**

```javascript
//后++，不参与操作符的运算
var num=10;
sum=num++ +10;
console.log(sum)//20
console.log(num)//11
//前++,参与操作符的运算
var num=10;
sum= ++num +10;
console.log(sum)//21
console.log(num)//11
```

##### 流程控制的三种结构

顺序控制 ：从上到下，从左到右边的顺序执行。

分支结构：if与switch..case

循环结构：while 与 do..while

```javascript
//用于提示输入框，默认获取的数据是字符串，需要进行类型的转化
var age = parseInt(prompt("请您输入您的年龄"))
console.log(age)
```

**在大多数的情况下，if-else的表达式都可以使用三元运算符来表示。**

```javascript
//三元运算符的表达式 var result = 表达式1?表达式2:表达式3；
var age = parseInt(prompt("请您输入您的年龄"))
var result = age > 18 ? "成年了" : "未成年";
console.log(result);
```

**多重if else语句**

```javascript
//多重if else语句
    var age = parseInt(prompt("请您输入您的年龄"))
    if(age>=20){
        ...
    }else if(条件){
        ...
    }else {
        ...
    }
```

**练习:计算闰年**

```javascript
//多重if else语句
//如何判断闰年还是平年
var year = parseInt(prompt("请您输入年份"))
if(year%4==0&&year%100!=0||year%400==0){
	console.log("闰年");
}else {
	console.log("平年");
}
```

##### switch case语句

```javascript
 //switch case语句
    var level="C";
    switch (level){
        case "A":console.log("90-100");break;
        case "B":console.log("80-90");break;
        case "C":console.log("70-80");break;
        case "D":console.log("60-70");break;
        default:console.log("未知的等级")
    }

```

default关键字可以省略，并且其后面的break关键字也是可以省略的。

switch-case采用的是严格模式。必须类型，值都是一一匹配的才可以。

**switch-case的巧用**

```javascript
 //switch case语句的巧用
    month = 10;
    switch (month) {
        case 1:
        case 3:
        case 5:
        case 7:
        case 8:
        case 10:
        case 12:console.log("有31天");break;
        case 4:
        case 6:
        case 9:
        case 11:console.log("有30天");break;
        case 2:console.log("有28天");break;
    }
```

##### while循环语句的使用**

```javascript
var i =0;
while(i<10){//循环的条件
	console.log(i);//code
	i++;//计数器
}
```

##### 作业部分如下:

**求6的阶乘**

**求1-100之间所有偶数的和**

**求1-100之间所有奇数的和**

**验证账号与密码的一致,登陆效果的实现**

**循环语句之do-while的循环**

```javascript
do{
    循环体;
}while(条件);
```

先执行一次循环体判断条件是否成立，成立则执行循环体，不成立就跳出循环。

##### for循环的使用

求1-100所有被3整除的数字的和。

```javascript
var sum=0;
  for(var i=1;i<=100;i++){
      if(i%3==0){
            sum+=i;
      }
  }
console.log(sum);
```

**嵌套for循环-画星星**

```javascript
    for (var i = 0; i < 5; i++) {
        for (var j = 0; j < 5; j++) {
            document.write("*");
        }
        document.write("<br/>");
    }
```

**打印三角状的五角星**

```javascript
for (var i = 0; i <= 5; i++) {
        for (var j = 0; j <= i; j++) {//通过行号限制每行的输出列数
            document.write("*");
        }
        document.write("<br/>");
    }
```

**九九乘法表**

```javascript
for (var i = 0; i <= 9; i++) {
    for (var j = 0; j <= i; j++) {//通过行号限制每行的输出列数
        document.write(i + "*" + j + "=" + i * j);
        document.write("\t")
    }
    document.write("<br/>");
}
```



