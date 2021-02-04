### Promise源码实现解析

#### 基本的Promise
```
function Promise(fn) {
  var deferreds = [];
  var state = "pending";
  var value = "";

  //事件队列中添加回调,在执行then方法时需要对状态进行判断，resolve执行之后再调用then方法state的状态就转变为fulfilled，这时直接执行回调函数即可，不用往事件队列中添加了
  this.then = function(deferred) {
    if (state === "pending") {
      deferreds.push(deferred);
      return this;
    }
    deferred(value);
    return this;
  }

  //延迟执行队列中的事件，resolve执行之后将state变更为fulfilled（完成态）
  function resolve(newValue) {
    value = newValue;
    state = "fulfilled";
    setTimeout(() => {
      deferreds.forEach(deferred => {
        deferred(value);
      })
    }, 0)
  }

  //执行实例化传入的回调函数，并将resolve传入
  fn(resolve);
}
```

```
//基于以上构造函数的demo
var p = new Promise((resolve) => {
  setTimeout(() => {
    resolve(123);
  }, 0)
});
p.then((res) => {
  console.log(res + '1');
})
p.then((res) => {
  console.log(res + '2');
})
//结果是1231和1232，该demo是将resolve()通过setTimeout放在任务队列的最后位置，通过then方法将回调函数放在Promise中定义的事件队列中
```

#### 串行Promise的实现
```
function Promise(fn) {
    //定义promise的初始状态
    var state = 'pending',
        value = null,
        //定义事件队列，存放then()方法的回调函数
        deferreds = [];
    //该方法返回一个新的promise实例，新的promise实例会通过handle函数将then方法中传入成功完成时的回调、失败时的回调和中间promise实例的resolve、reject方法封装成一个对象作为参数传入
    this.then = function (onFulfilled, onRejected) {
        return new Promise(function (resolve, reject) {
            handle({
                onFulfilled: onFulfilled || null,
                onRejected: onRejected || null,
                resolve: resolve,
                reject: reject
            });
        });
    };
    
    function handle(deferred) {
          //判断状态，如果state=pending那么就将then传入的回调和中间promise的resolve、reject封装的对象放到事件队列中。
        if (state === 'pending') {
            deferreds.push(deferred);
            return;
        }
        //如果state=fulfilled或者state=rejected，得到要执行的回调函数
        var cb = state === 'fulfilled' ? deferred.onFulfilled : deferred.onRejected,
            ret;
        //如果then中没有添加回调，那么根据状态直接执行中间promise的resolve或者reject函数    
        if (cb === null) {
            cb = state === 'fulfilled' ? deferred.resolve : deferred.reject;
            cb(value);
            return;
        }
        //执行then中添加的回调，获取到结果
        ret = cb(value);
        //调用中间promise的resolve，并将then中回调函数的执行结果传入
        deferred.resolve(ret);
    }

    function resolve(newValue) {
        //如果传入resolve函数的参数是一个promise实例，那么直接执行该promise实例的then方法
        if (newValue && (typeof newValue === 'object' || typeof newValue === 'function')) {
            var then = newValue.then;
            if (typeof then === 'function') {
                then.call(newValue, resolve, reject);
                return;
            }
        }
        //将promise的状态变更为fulfilled
        state = 'fulfilled';
        //将新的值赋给全局的value
        value = newValue;
        finale();
    }

    
    function reject(reason) {
        state = 'rejected';
        value = reason;
        finale();
    }

    function finale() {
        //通过异步的方式调用事件队列中在执行then阶段放入的回调事件
        setTimeout(function () {
            deferreds.forEach(function (deferred) {
                //通过调用handle（通过执行resolve，这里执行handle时，状态一定变更为fulfilled了）
                handle(deferred);
            });
        }, 0);
    }
    
    //实例化promise时会执行该函数
    fn(resolve, reject);
}
```

```
//基于串行promise实现的demo
function getUserId () {
  return new Promise((resolve) => {
    setTimeout(() => {
      resolve(1000);
    }, 0)
  })
}

function getMobileByUserId (userId) {
  return new Promise((resolve) => {
    setTimeout((userId) => {
      console.log('根据用户id'+ userId +'获取用户的手机号');
      resolve(13716487020);
    }, 0)
  })
}

getUserId().then(getMobileByUserId).then(res => {
  console.log(res);
});

```