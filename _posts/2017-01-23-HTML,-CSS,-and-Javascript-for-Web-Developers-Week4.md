---
layout: post
title: HTML, CSS, and Javascript for Web Developers Week4
subtitle: 
date: 2017-01-23
categories: Coursera
tags: [Web]
catalog: true
---

#### Javascript Basics

早该写没有写笔记，为了避免忘记了学过什么还是写一下笔记好了\_(:з」∠)_其实我已经忘了(.\_.)

Variables is always start with 'var'

```javascript
var msg = "hi";
```

 **Dynamically typed language**:  No type needed

```javascript
function a(x, y){...} // arguments without 'var', if arguments are redundant, it won't cause error but not used
var a = function (){} // the same as above, a is a function
```

**Scope chain**:  Referenced(not defined) var will be searched for in its current scope first, if not found then outer scope will be searched unitl go to the Global scope. If not found at last, it is undefined.

**Types**: 6 primitive and 1 Object

Object type: a collection of name/value pairs.

Primitive: single, immutable value -> Boolean, Undefined, Null, Number(always double precision 64 bit floating point), String, Symbol

**Primitives are passed by value, objects are passed by reference**

Function constructors:

```javascript
// Function constructos, always use name begin with Uppercase 
function Circle (radius){
  this.r = radius;
  // doesn't return anything
  this.getArea = // shuold not put here
    function() {return Math.PI*Math.pow(this.r, 2);};
}
// should put here, same memory space 
Circle.proptype.getArea = 
  function(){return Math.PI*Math.pow(this.r, 2);};
var myCircle = new Circle(10); // a new object
console.log(myCircle); // Circle{r: 10} 
console.log(myCircle.getArea()); // 314.15926.... 
```

#### JS & HTML

```html
<!doctype html>
<html lang="en">
  <head>
  	<meta charset="utf-8">
    <script src="js/script1.js"></script>
    <script src="js/script2.js"></script>
    <script src="js/script3.js"></script>
  </head>
  <body>
    <h1>╮(╯▽╰)╭</h1>
  </body>
</html>
```

Script1/2/3 will overwrite vars in gloabl scope and load vars in gload scope.

Fake Namespaces: -> the variables function refers to can be defined in separate files and won't influence each other.

Immediately Invoked Function Expression: 自执行

```javascript
(function (name){
  var name = name;
  function print(){console.log("Hello"+ this.name);}
})();
// pass to the window object
// so the outside and inside will not collide
(function (window) {
  var johnGreeter = {};
  johnGreeter.name = "John";
  var greeting = "Hi ";
  johnGreeter.sayHi = function () {
    console.log(greeting + johnGreeter.name);
  }

  window.johnGreeter = johnGreeter;

})(window);
```

[Assignment4](https://triangleweb.github.io/WebLearning/mod4_solution/)

又一个勉强完成



