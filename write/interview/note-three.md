## 常见面试题

```
LazyMan('Tony');
// Hi I am Tony

LazyMan('Tony').sleep(10).eat('lunch');
// Hi I am Tony
// 等待了10秒...
// I am eating lunch

LazyMan('Tony').eat('lunch').sleep(10).eat('dinner');
// Hi I am Tony
// I am eating lunch
// 等待了10秒...
// I am eating diner

LazyMan('Tony').eat('lunch').eat('dinner').sleepFirst(5).sleep(10).eat('junk food');
// Hi I am Tony
// 等待了5秒...
// I am eating lunch
// I am eating dinner
// 等待了10秒...
// I am eating junk food

该题考查自己封装一个事件队列，并且不同的事件放在队列的不同位置

class LazyManClass {

    constructor(name) {
        this.queue = [];
        console.log(name);
        //在构造函数中添加异步函数，为的是等待事件队列中已经将调用事件都添加到队列中了
        setTimeout(() => {
            this.tick();
        })
    }

    //定义事件并添加到事件队列的末尾
    eat(name) {
        var that = this;
        var fn = (function(name) {
            return function() {
                console.log(name);
                that.tick();
            }
        })(name);
        this.queue.push(fn);
        return this;
    }

    //定义事件添加到事件队列的队首
    sleepFirst(sec) {
        var that = this;
        var fn = (function(sec) {
            return function() {
                setTimeout(() => {
                    console.log(`wait for ${sec} s`);
                    that.tick();
                }, sec * 1000) 
            }
        })(sec);
        this.queue.unshift(fn);
        return this;
    }

    //定义事件添加到队列的队尾
    sleep(sec) {
        var that = this;
        var fn = (function(sec) {
            setTimeout(() => {
                console.log(`wait for ${sec} s`);
                that.tick();
            }, sec * 1000)
        })(sec);
        this.queue.push(fn);
        return this;
    }

    tick() {
        var fn = this.queue.shift();
        fn && fn();
    }
} 

var LazyMan = function(name) {
    return new LazyManClass(name);
}

LazyMan('Tony').eat('lunch').eat('dinner').sleepFirst(5).sleep(10).eat('junk food');
```

/**
看源码，整理 Vue 与 React 框架的所有横向对比，包括渲染原理、虚拟 dom、diff、patch、fiber、批量更新，手写响应式，框架用到的模式、设计思想，性能优化，相关生态技术等等。
webpack 原理、热更新原理、动态加载原理、常见 plugins、loader、常见优化，怎么打包、怎么分 chunk，怎么写一个 plugins，生命周期，微内核源码等内容，以及 rollup、gulp 的使用、应用场景。
跨端框架的研究，工程化的梳理，使用的技术栈的坑，移动端的一些实践，面试时额外准备的项目复盘，竞品调查，对方产品的资料，测试系列，还有很多如微前端、中台、serverless、可视化、Wasm 等就不举例了。

input type 都有哪些类型，还记得其他 attrs 呢
css 的伪类和伪元素有哪些？有什么区别？
在一个未知宽度的父元素内如何创建一个等边正方形
异步加载 js 会阻塞什么
数组所有方法都有哪些？findIndex 的参数说明
vue 和 react 的异同
如何优化 vue 框架，注意是优化框架
vue 和 react 的 jsx 使用
id key 真的能使列表比对更高效吗？举个反例？
webpack 优化的手段
tree-shaking 怎么配置，如何 避免 tree-shaking？
electron 和小程序遇到什么坑？
说下微信自动化测试
es2015 到 es2020 的新特性，你最常用什么，给你收益最大的。
weakMap 和 Map 的区别，weakMap 原理，为什么能被 GC？
如何干扰 GC ？
webpack import 动态加载原理
知道 webpack 中的 devTool 吗？
如何进行错误定位和数据上报，线上异常的处理
为什么有时候配置了 webpack caching，chunk 还是更新了？
讲讲浏览器和 node 的 eventloop
微任务后面还有哪些？requestAnimationFrame 是怎么调用的？requestAnimationFrame 帧内总是有任务吗？分情况说下。
帧数怎么计算？
了解网络安全吗？
如何避免数据被 iframe 截获
说下状态码
说下 304，什么情况会 304？协商缓存的头部字段？
*/