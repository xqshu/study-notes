# call，apply，bind

> shuxiaoqin, 2016.9.29

### 对比

方法|是否执行函数|参数|调用方式
----|------------|----|--------
call|是 |(context, arg1, arg2, ...)|fn.call(context, arg1, arg2, ...)
apply|是 |(context, [arg1, arg2, ...])|fn.call(context, [arg1, arg2, ...])
bind|否 |(context)|var newFn = fn.call(context); newFn();

## 示例
### call

``` js
var a = {x: 1};
var b = function (y, z) {
    console.log(this.x + y + z)
};

// 此时b的this（即b执行时的上下文）被改变为a，
// 因此this.x为1，第二个参数之后都是传给b实参。
b.call(a, 3, 4); // output: 8; 
```

### apply

``` js
var a = {x: 1};
var b = function (y, z) {
    console.log(this.x + y + z)
};
b.apply(a, [3, 4]); // output: 8
```

### bind

``` js
var a = {x: 1};
var b = function (y, z) {
    console.log(this.x + y + z)
};
var newB = b.bind(a); // b的this（执行的上下文）改变为a，生成一个新函数
newB(3, 4); // 函数执行
```

## 应用场景
### 数组追加

```js
var array1 = [12 , "foo" , {name: "Joe"} , -2458];  
var array2 = ["Doe" , 555 , 100];  
Array.prototype.push.apply(array1, array2);  
// 等同于 array1 = array1.concat(array2);
console.log(array1);
// array1 值为  [12 , "foo" , {name "Joe"} , -2458 , "Doe" , 555 , 100]
```

### 获取数组的最大（小）值
```js
var numbers = [5, 458 , 120 , -215 ];  
console.log(Math.max.apply(Math, numbers)); // 458
console.log(Math.max(5, 458 , 120 , -215)); // 458
```

### 类（伪）数组使用数组方法，如arguments

```js
// 打印函数的最后一个参数
var fn = function () {
    // 将arguments（伪数组）转成正常数组
    var args = Array.prototype.slice.call(arguments);  
    console.log(args.pop());
}

fn(4, 5, 6);
```

#### 一道面试题

实现一个log函数，模拟简单的控制台输出

```js
function log(msg) {
    console.log(msg);
}

// 更优秀的面试者会这么写
function log() {
    console.log.apply(console, arguments);
}

log('hello', 'world');  // hello world
```

如果需要在输出结果时加上前缀，像下面这样：
``` js
'(app) hello world'
```

log函数可以这么实现
``` js
function log() {
    var args = arguments;
    args = Array.prototype.slice.call(args);
    args.unshift('(app)');
    console.log.apply(console, args);
}

log('hello', 'world');  // (app) hello world
```
