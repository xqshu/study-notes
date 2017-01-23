
```js
var A = function () {
    this.a = 1;
    this.m = function (b) {
        return this.a + b;
    }
}

A.prototype = {
    a: 2, 
    b: 3
};

var o = new A();
var p = Object.create(o);

p.a = 3;

console.log(o.m(1));    // 2
console.log(p.m(1));    // 4

delete p.a;
console.log(p.m(1));    // 2

delete o.a;
console.log(p.m(1));    // 3
```
