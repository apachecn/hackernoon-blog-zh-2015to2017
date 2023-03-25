# Javascript:函数的性能测量

> 原文：<https://medium.com/hackernoon/javascript-nodejs-react-performance-measurement-test-check-injection-overwrite-class-object-67a236f3e102>

![](img/2fb6a3f3a19cbd497129252b5373b94c.png)

measurement

在 web 开发中，加载速度和交互速度是良好用户体验的关键。众所周知，当处理的数据量很大时，功能性能测量甚至更为重要，一个糟糕的功能设计确实会带来糟糕的用户体验，而且可能会导致浏览器崩溃。

## 函数的集合

我们将测量名为`*obj1*`的对象中的每一个函数

```
const obj1 = {
    **fun**: (max = 1000) => {
            let count = 0
            for( let i =0; i<max; i++){count +=1}
            return count
        }
}
```

## 性能注入

```
var temp = obj1['fun']
obj1['fun'] = function(){ /*_*_*_*_ Init _*_*_*/
    var old_time = new Date(); var returnResult = temp.apply(this,arguments); /*_*_*_*_ Finished _*_*_*/
    var new_time = new Date(); console.log(`*_*_*_ function ${fun} _*_*_*`)
    console.log('Spend time: ', (new_time - old_time), 'ms')
    return returnResult
}
```

# 结果

当我们运行该函数时，性能记录如下:

```
obj1.fun(1000)
```

末端的

```
*_*_*_ function funCallback _*_*_*
           RAM         :  0.28125 mb
           HeapTotal   :  0.5 mb
           HeapUsed    :  0.29491424560546875 mb
           External    :  0 mb
           CPU         :  0.00137 s
           Spend time  :  2 ms
```

# git 上的例子

```
$ git clone [https://github.com/wahengchang/javascript-function-performance-measurement](https://github.com/wahengchang/javascript-function-performance-measurement)$ npm run install
$ node example
```

# 参考

[https://github . com/wahengchang/JavaScript-function-performance-measurement/blob/master/readme . MD](https://github.com/wahengchang/javascript-function-performance-measurement/blob/master/README.md)