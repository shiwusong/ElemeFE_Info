# js中this的指向。

__最重要的一点就是：this永远指向运行它的那个对象。__
当然，这只是一般情况，还存在一些诱惑性的情况，比如`call`、`apply`、`bind`、`箭头函数`、`class`。  
以下代码都是node环境下运行。

## 1.常见的困惑
``` javascript
x = 'this is node global';
this.x = 'this is global';
let obj = {
  x:'this is obj',
  a:function(){
    console.log(this.x);
  }
}
function b(){
  console.log(this.x);
}
obj.a(); // this is obj
b(); // this is node global
let c = obj.a; 
c(); // this is node global
```

## 2.call和apply
具体的资料太多了，我感觉call和apply只是传参方式不同，都是用来修改this指向的。
``` javascript
x = 'this is node global';
this.x = 'this is global';
let obj = {
  x:'this is obj',
  a:function(){
    console.log(this.x);
  }
}
function b(){
  console.log(this.x);
}
obj.a(); // this is obj
b.call(obj); // this is obj
let c = obj.a; 
c.apply(this); // this is global
```

## 3.bind和箭头函数
```javascript
x = 'this is node global';
this.x = 'this is global';
let obj = {
  x:'this is obj',
  a:function(){
    console.log(this.x);
  }.bind(this),
  b:()=>{
    console.log(this.x);
  }
}
function b(){
  console.log(this.x);
}
obj.a(); // this is global
obj.b(); // this is global
obj.a.call(obj); // this is global
obj.b.call(obj); // this is global

b(); // this is node global
b.call(obj); // this is obj
let bb = b.bind(this);
bb.call(obj); // this is global
```
这样的结果对于刚开始全面了解this的我还是很惊讶的。  
1.函数如果bind或者是箭头函数，那么call和apply将不能改变this。（但我暂时不知道内部原理）  
2.node运行js时，如果指定了this那么this指向的是当前脚本而不是全局对象。

4.类
``` javascript
x = 'this is node global';
this.x = 'this is global';

class A {
  constructor() {
    this.x = 'this is A';
    this.showx = this.showx;
  }
  showx() {
    console.log(this);
  }
}
let a = new A();
let showa = a.showx;
showa(); // undefined



function B() {
  this.x = 'this. is B';
  this.showx = function(){
    console.log(this.x);
  };
}
let b = new B();
let showb = b.showx;
showb(); // this is node global
```
很奇怪为什么第一个是undefined？