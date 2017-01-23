# 匿名函数与闭包

## 匿名函数

没有名称的函数就是匿名函数

```js
function (argument1, argument2){
}
```

## 闭包

闭包通常来讲可以简单的认为是可以访问一个函数里面的局部变量的另外一个函数。
闭包函数体在外部函数执行的时候是不执行的。

```js
function a() {
   var i = 1;
   function b() {
       ++i;
      return i;
   }
  return b;
}
var c = a();
console.log(c());   // 2
console.log(c());   // 3
```
用一个专业一点的说法就是：函数调用返回后一个没有释放资源的栈区；

一般，当函数执行完毕后，局部活动对象会被销毁，内存中仅保存全局作用域，
但闭包的情况是不一样的。闭包的活动对象依然会保存在内存中，于是像上例中，
函数调用返回后，变量i是属于活动对象里面的，就是说其栈区还没有释放，
但你调用c()的时候i变量保存的作用域链从b()->a()->全局去寻找作用域var i声明所在，
然后找到了var i=1;然后在闭包内++i;结果，最后输出的值就是2了；不知道这么说有没
人明白，如果不明白，那么只要记住它的闭包的两个点就好了，一点就是闭包的活动对象
没有被销毁；第二点是作用域链的关键是他要遇到var 声明。

作用域链的这种配置机制引出了一个值得注意的副作用，即闭包只能取得包含函数中任何
变量的最后一个值。别忘了闭包所保存的是整个变量对象，而不是某个特殊的变量。
下面这个例子可以说明这个问题：

```js
function test(){  
    var arr = [];  
    for(var i =0; i< 5; i++){  
        arr[i] = function(){  
            return i;  
        }  
    }      
    return arr;       
}  
var fa = test();  
  
console.log(fa[0]);  // 返回函数
console.log(fa[0]()); // 5 ?  
```
这个函数会返回一个函数数组。表面上看，似乎每个函数都应该返回自己的索引值，
即位置0的函数返回0，依此类推。但实际上，每个函数都返回5。因为每个函数的作用域链中
都保存着test()函数的活动对象，所以他们引用的都是同一个变量 i。当函数test()返回后，
变量i的值是5，此时每个函数都引用着保存变量 i 的同一个变量对象，所以在每个函数内部
i的值都是5。
但是我们可以通过创建另一个匿名函数强制让闭包的行为符合预期，如下所示：

```js 
function test(){  
    var arr = [];  
    
    for(var i =0; i< 5; i++){  
        arr[i] = (function(num){  
            return function(){  
                return num;  
            }  
              
        })(i);  // 匿名函数及时也执行了
    }
    return arr;       
}  
var fa = test();  
  
console.log(fa[0]);  // 返回函数
console.log(fa[0]()); // 0，正常了   
```

## 经典面试题

1. 实现一个函数add(1)(2) = 3; add(1)(2)(3) = 6; 依次类推

```js
function add(x) {
    var sum = x;
    var tmp = function (y) {
        sum = sum + y;
        return tmp;
    };
    tmp.toString = function () {
        return sum;
    };
    return tmp;
}
console.log(add(1)(2)(3) + 0);  //6
console.log(add(1)(2)(3)(4));   //10

// 或者
function add(x) {
    var sum = x;
    var tmp = function (y) {
        sum += y;
        return tmp;
    };
    tmp.valueOf = function () {
        return sum;
    };
    return tmp;
}
console.log(add(1)(2)(3) + 0);  //6
console.log(add(1)(2)(3)(4));   //10
```
首先要一个数记住每次的计算值，所以使用了闭包，在tmp中记住了x的值，第一次调用add(),
初始化了tmp，并将x保存在tmp的作用链中，然后返回tmp保证了第二次调用的是tmp函数，
后面的计算都是在调用tmp, 因为tmp也是返回的自己，保证了第二次之后的调用也是
调用tmp，而在tmp中将传入的参数与保存在作用链中x相加并付给sum，这样就保证了计算；

但是在计算完成后还是返回了tmp这个函数，这样就获取不到计算的结果了，我们需要的结
果是一个计算的数字那么怎么办呢，首先要知道JavaScript中，打印和相加计算，会分别
调用toString或valueOf函数，所以我们重写tmp的toString和valueOf方法，返回sum的值。

2. 已知object变量如下：
var obj = {
    a: {
        b: {
            c: 6
        }
    }
};
实现查找运算，如obj.find('a')('b')('c') = 6; obj.find('a')('d') = undefined

```js
var obj = {
    a: {
        b: {
            c: 6
        }
    }
};

Object.prototype.find = function (o) {
    var tmp = this;
    
    var fn = function (oo) {
        tmp = tmp[oo];
        if (typeof(tmp) !== 'object') {
            return tmp;
        }
        else {
            return fn;
        }
    };
    
    return fn(o);
}
```
## 相关链接
http://www.ruanyifeng.com/blog/2009/08/learning_javascript_closures.html?20120612141317#comments