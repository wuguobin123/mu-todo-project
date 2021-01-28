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