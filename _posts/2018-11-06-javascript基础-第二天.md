---
layout:     post
title:      javascript基础第二天
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

#### 2个关键字

##### break 关键字

终止当前的循环。

##### continue 关键字

跳出本次循环。

```javascript
var sum=0;
    var i=100;
    while(i<=200){
        if(i%2==0){
            i++;//计数要在continue前面进行，特别是在while循环当中。
            continue;
        }
        sum+=i;
        i++;
    }
    console.log(sum);
```

**continue的案例**

求出1-100的累加值，前提是跳出所有个位数为3的数字。

**个位为3的意思就是数字%10的余数为3**

```javascript
var sum=0;
    var i=1;
    while(i<=100){
        if(i%3==0){
            i++;
            continue;
        }
        sum+=i;
        i++;
    }
console.log(sum);
```

#### 数组

##### 数组的创建方式

**通过构造函数的方式创建数组**

```javascript
var array=new Array();
var array=new Array(10);//指定数组的长度,数组中的每个元素的数据类型为underfined
var array=new Array(10，20,30,40,80);//指定数组中元素的个数,数组中的元素可以重复
```

**通过字面量的方式创建数组**

##### 数组的循环遍历

**array.length方法获取数组的长度**

```javascript
var array=[10,20,30,10,50];
    for(var i=0;i<array.length;i++){
        console.log(array[i]);
}
```

**求数组中所有元素的和与平均值**

```javascript
var array=[10,20,30,40,50];
  var sum=0;
  for(var i=0;i<array.length;i++){
      sum+=array[i];
  }
console.log(sum);
console.log(sum/array.length);
```

**求数组中元素的最大值**

==通过定义个最大值，利用这个最大值跟数组中的每个元素进行比较进行得出==

```javascript
var array=[10,20,30,40,50,90,78,12,20];
  var max=Number.MIN_VALUE;//定义个最大值为js中的最小数值
    for(var i=0;i<array.length;i++){
        if(max<array[i]){
            max=array[i];
        }
    }
    console.log(max);
```

**倒叙遍历数组**

```javascript
var array=[10,20,30,40,50];
for(var i=array.length-1;i>=0;i--){
    console.log(array[i]);
}
```

**把数组中的每个元素拼接到一起并产生一个字符窜**

```javascript
var array=["我爱罗","佐助","鸣人","卡卡西","小樱"];
var str="";
for(var i=0;i<array.length;i++){
    str+="|"+array[i];
}
    console.log(str)
```

**去掉数组中重复的0，并把数组返回**

==难点：基本思路就是把所有非0的元素放到新数组中，但是有个问题就是：新数组的索引问题该怎么解决？==

```javascript
var array=[1,8,0,20,90,2,0,89];
//思路 把非0的元素放到新数组中
//对于新数组的索引该怎么解决的问题
var newArray=[];
for(var i=0;i<array.length;i++){
    if(array[i]!=0){
        newArray[newArray.length]=array[i];//难点在这里
    }
```

**数组的反转**

==基本就是将数据里面的元数据倒过来，我需要考虑的问题是:==

==基本思路就是:第一个位置和最后一个位置对调，第二个位置与倒数第二个位置对调，，，，==

==程序怎么知道要循环多少次去对调数据呢？==

==要按照这个思路去找出里面的数学规律就好解决了。==

```javascript
var array=[1,8,0,20,90,2];
var temp;
for(var i=0;i<array.length/2;i++){//控制交换的次数
    temp=array[i];
    array[i]=array[array.length-1-i];//交换数组中索引位置的规律
    array[array.length-1-i]=temp;
}
    console.log(array)
```

#### 冒泡排序

##### 什么是冒泡排序？

数组中的数据按照从小到大或者从大到小的顺序或者按照一定的顺序进行排列的方式就是冒泡排序。

##### 冒泡排序程序编写

```javascript
var array=[1,8,0,20,90,2];
//基本思路:
//第一轮 第一个元素与第二个元素比较(按照从小到大的顺序排列),如果第一个元素比第二个元素大，调换顺序，调换后，再用第二个元素与第三个元素比较，，，
//第二轮 第一个元素依次与每个元素进行比较，，，
//外层控制比较的轮数,循环的轮数在依次增大
//内层控制每轮比较的次数,依据规律发现每轮的循环次数都在按照一定规律减少
for(var i=0;i<array.length-1;i++){
    for(var j=0;j<array.length-1-i;j++){
        //判断 比较前面元素与后面元素的大小
        if(array[j]>array[j+1]){
            var temp=array[j];
            array[j]=array[j+1];
            array[j+1]=temp;
        }
    }
}
    console.log(array);
```

#### 函数

##### 注意

函数的命名采用驼峰命名的办法。

函数一旦重名，后面的重名函数会将前面的函数覆盖。

##### 函数的参数

函数的参数就是定义变量与传递变量值到函数内部的。

##### 形参与实参

形式参数：函数在定义的时候小括号里面的变量就是形参

实际参数 : 函数在被调用的时候传入小括里面的值就是实参，可以是实际的值也可以是变量。

==形参与实参的个数是可以不一致的==

##### 返回值

没有返回值的函数不是一个完整的函数，函数的返回值的关键字为`return`.





