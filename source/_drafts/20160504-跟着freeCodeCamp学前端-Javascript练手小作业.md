---
title: 跟着freeCodeCamp学前端-Javascript练手小作业
date: 2016-04-17 17:50:30
categories: Javascript
tags: [Javascript]
---


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

