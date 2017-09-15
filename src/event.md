# 移动端事件
主要有touchstart，touchmove，touchend，click等
### tap事件原理
在移动端中,由于两次触摸是放大操作,,所以当你点击一次的时候,浏览器会等待300ms,看用户是否会进行第二次点击,如果没有的话,才会执行点击事件。
tap事件的原理其实是源于触摸touch事件,在移动触摸事件就是在同个点触发,及touchmove的坐标距离touchstart的坐标距离为0,并且点击的时间不超过某个设定的时间值,超过该时间值的话,就属于长按了。
[参考链接](http://www.cnblogs.com/cythia/p/6928364.html)

### [tap事件点透原理](http://www.cnblogs.com/wqhwe/p/5630557.html)
