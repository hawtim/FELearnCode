# 如何编写高质量的函数

## 计算机组成原理

内存分为好几个区域，代码区域，栈区域，堆区域

## 为什么会存在序列化和反序列化？

## 将字符串变成真正的 js 代码

每一个函数调用，都会在函数上下文堆栈中创建帧

开始执行函数
执行函数是在栈上完成的

1. 会形成一个供代码执行的环境，也是一个栈内存
2. 将存储的字符串复制一份到新开辟的栈内存中，使其变成真正的 js 代码
3. 先对形参进行复制，在进行变量提升，比如将 var function 变量提升
4. 在这个新开辟的作用域中自上而下执行
5. 将执行结果返回给当前调用的函数

计算机中最本质的闭包解释
函数在执行的时候，都会行程一个全新的私有作用域，也叫私有栈内存
目的是把原有堆内存中存储的字符串变成真正的js代码
保护该栈内存的私有变量不收外界的干扰
函数执行的这种保护机制，在计算机中称之为 闭包 。

## 探索 js 引擎工作原理

执行环境栈
全局对象
执行环境
变量对象
活动对象
作用域
作用域链

```js
var x = 1 // 定义一个全局变量 x
function A(y) {
    var x = 2 // 定义一个局部变量 x
    function B(z) { // 定义一个内部函数 B
        console.log(x+y+z)
    }
    return B // 返回函数 B 的引用
}
var C = A(1) // 执行函数A，返回 B 的引用
C(1)

```

以上例子，我们从全局初始化，执行函数A，执行函数B 三个阶段来分析 js 引擎的工作机制

### 全局初始化

```js
var globalObject = {
    Math: {},
    String: {},
    Date: {},
    document: {},
    window: this
}
```
