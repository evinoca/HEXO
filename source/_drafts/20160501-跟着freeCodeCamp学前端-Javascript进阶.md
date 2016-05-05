---
title: 跟着freeCodeCamp学前端-Javascript进阶
date: 2016-04-17 17:50:30
categories: Javascript
tags: [Javascript]
---

# 面向对象

在看基础的范畴上，感觉JS跟python什么的没有什么不一样。

都是语法简单的脚本语言，可以实现简单的面向对象。

## 类声明

类声明的方式类似于声明一个变量，或者说像是perl里的hash表或者是python的字典数据。

与其说是一个类，更像是一个枚举。

```javascript
var car = {
  "wheels":4,
  "engines":1,
  "seats":5
};

var motorBike = {

  // Only change code below this line.
  "wheels":4,
  "engines":1,
  "seats":5
};

```

## 构造器声明

这个就很有意思了，在我看来是一个可以访问参数的方法。

```javascript

var Car = function() {
  this.wheels = 4;
  this.engines = 1;
  this.seats = 1;
};

// Only change code below this line.

var MotorBike = function() {
  this.wheels = 4;
  this.engines = 1;
  this.seats = 1;
};

```



## 实例化

通过传参数来实例化一个对象。

与Python对象的的______init______()方法差不多一个意思吧...

```javascript
var Car = function(wheels, seats, engines) {
  this.wheels = wheels;
  this.seats = seats;
  this.engines = engines;
};

//Try it out here
var myCar;
myCar = new Car(4,3,2);
```

## 私有参数

在一个对象中使用this来声明可以被外部访问的变量，

如果直接var出来的，就只能被自己访问。

```javascript

var Car = function() {
  // this is a private variable
  var speed = 10;

  // these are public methodsque
  this.accelerate = function(change) {
    speed += change;
  };

  this.decelerate = function() {
    speed -= 5;
  };

  this.getSpeed = function() {
    return speed;
  };
};

var Bike = function() {

  // Only change code below this line.
  var gear=0;
  this.getGear=function() {
    return gear;
  };
  this.setGear=function(gearInput){
    gear = gearInput;
    return gear;
  };
};

var myCar = new Car();

var myBike = new Bike();


myBike.setGear(4);
myBike.getGear();
myBike.setGear(3);
myBike.getGear();
myBike.setGear(1);
myBike.getGear();
```



# Javascript的数组操作

## Array.map() 映射函数

```javascript

var oldArray = [1,2,3,4,5];

// Only change code below this line.

var newArray = oldArray.map(function(val){
  return val +3;
});
```



## Array.reduce() 递归函数 

```javascript
var array = [4,5,6,7,8];
var singleVal = 0;

// Only change code below this line.
var singleVal = array.reduce(function(previousVal, currentVal) {return previousVal + currentVal;}, 0);


```

## Array.filter() 过滤器函数

```javascript

var oldArray = [1,2,3,4,5,6,7,8,9,10];

var newArray = oldArray.filter(function(val) {
  return val < 6;
});

```

## Array.sort() 排序函数

```javascript
var array = [1, 12, 21, 2];

// Only change code below this line.
var array =array.sort(function(a, b) {
  return [b - a];
});

```

result: [21,12,2,1]

## Array.concat() 链接

```javascript

var oldArray = [1,2,3];
var newArray = [];

var concatMe = [4,5,6];

// Only change code below this line.

newArray = oldArray.concat(concatMe);//newArray = oldArray.concat(concatMe);

```

# 练手

## Reverse a String

>Return the provided string with the first letter of each word capitalized. Make sure the rest of the word is in lower case.
>
>For the purpose of this exercise, you should also capitalize connecting words like "the" and "of".



```javascript
function reverseString(str) {
  var arr =  str.split("").reverse().join("");  
  return arr;  
}

reverseString("hello");
```



## Find the Longest Word in a String

>Return the length of the longest word in the provided sentence.
>
>Your response should be a number.
>
>Remember to use [Read-Search-Ask](https://github.com/FreeCodeCamp/freecodecamp/wiki/How-to-get-help-when-you-get-stuck) if you get stuck. Write your own code.

```javascript

function findLongestWord(str) {
  var n = 0;
  var arr = str.split(" ");
  arr.forEach(function(item){
    if (item.length>n){
      n = item.length;}});
  return n;
}

findLongestWord("The quick brown fox jumped over the lazy dog");

```



## Title Case a Sentence

```javascript

function titleCase(str) {
  var ret=[];
  var arr=str.split(" ");
  var a;
  arr.forEach(function(e){
    e=e[0].toUpperCase()+e.substr(1).toLowerCase();
    ret=ret.concat(e);
  });
  
    
  return ret.join(" ");
}

titleCase("I'm a little tea pot");

```



## Sum All Numbers in a Range

We'll pass you an array of two numbers. Return the sum of those two numbers and all numbers between them.

The lowest number will not always come first.

```javascript

function sumAll(arr) {
  var targetArr = [];
  var max = Math.max(...arr);
  var min = Math.min(...arr);
  
  for (i=min; i <= max; i++){
    targetArr=targetArr.concat(i);
  }
  
  
  var sum = targetArr.reduce(function(a, b){ return a + b; });  
  return sum;
}

sumAll([1, 4]);
sumAll([4, 1]);
sumAll([10, 5]);
```



Mozilla JS手册：[https://developer.mozilla.org/en-US/docs/Web/JavaScript](https://developer.mozilla.org/en-US/docs/Web/JavaScript)

