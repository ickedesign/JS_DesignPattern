## this
> this的指向大概可以分成四种：

- 作为对象的方法调用
- 作为普通函数调用
- 构造器调用
- `Function.prototype.call`或`Function.prototype.apply`调用

### 1. 作为对象的时候，<strong>this指向该对象</strong>


```js
var obj = {
	a: 1,
	getA: function() {
		alert(this === obj);//true
		alert(this.a);//1
	}
};

obj.getA();

```

### 2. 作为普通函数调用，<strong>this总是指向全局对象（在浏览器中就是window）</strong>

```js
window.name = 'globalName';

var getName = function() {
	return this.name;
};

console.log(getName());//globalName
```
或者
```js
window.name = 'globalName';

var myObject = function() {
	name: 'sven',
	getName: function() {
		return this.name;
	}
}

var getName = myObject.getName;
console.log(getName());//globalName
```
再例如局部的callback，它作为普通函数调用时，callback内部的this指向了windows对象，解决方法则可以用一个变量保存this：
```js
document.getElemmentById('div1').onclick = function() {
    var that = this;
    var callback = function() {
        alert(that.id); //div1;
    }
    callback();
}
```
在ES5的strict模式下，这种情况下的this已经不会指向全局对象了，而是指向undefined

```js
function func() {
	"use strict";
	alert(this);//undefined
};

func();
```
### 3. 构造器调用
当用`new`运算符调用函数时，该函数总会返回一个对象，通常情况`this`就是指向返回的这个对象，但如果构造器显示返回了一个object类的对象，那运算结果最终返回的是那个对象而不是this，例如

```js
var MyClass = function() {
	this.name = "sven";
};

var obj = new MyClass();
alert(obj.name);//sven
```

```js
var Myclass = function(){
	this.name = "sven"
	return {
		name: "anne"
	}
};

var obj = new MyClass();
alert(obj.name);//anne
```

### 4. Function.prototype.call或Function.prototype.apply可以动态的改变传入函数的this

```js
var obj1 = {
	name: "sven",
	getName: function() {
		return this.name;
	}
};

var obj2 = {
	name: 'anne'
};

console.log(obj1.getName());//sven
console.log(obj1.getName.call(obj2));//anne
```

> 用`getId`代替`document.getElementById `

`document.getElementById`方法的内部实现中需要用到`this`，这个`this`本来被期望指向`document`。当用`getId`来引用`document.getElementById`后，再调用`getId`，此时就变成了普通函数的调用。函数内部的`this`指向了`window`，而不是`document`

```html
<html>
<body>
  <div id="div1">你好</div>
</body>
</html>
```

```js
document.getElementById = (function(func) {
	return function() {
		return func.call(document);
	}
})(document.getElementById);

var getId = document.getElementById;
var div = getId('div1');

console.log(div.id);//div1
```

## call和apply

> call和apply的区别

传入参数不同，apply接受两个参数，第一个为this指向，第二个为一个带下标的集合，这个集合可以为数组也可以为类数组；call传入的参数数量不固定，第一个也是代表this指向，第二个开始往后每个参数被依次传入函数

```js
var func1 = function(a,b,c) {
  
  alert([a,b,c]);//[1,2,3]
}

func1.apply(null,[1,2,3]);

var func2 = function(a,b,c) {
  
  alert([a,b,c]);//[1,2,3]
}

func2.call(null,1,2,3);
```

> 使用call和apply有时是为了借用其它对象的方法

```js
Math.max.apply(null,[1,2,5,4,3]);//5
```
