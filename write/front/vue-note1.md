## 渲染流程

### 代码结构
```
//定义构造函数
function Vue() {

}
//原型上定义api
Vue.prototype.init = function() {

}
//函数上直接定义静态方法
Vue.util = {

}
```

### 初次渲染

```
//通过new Vue(options)的方式实例化，获取最外层的vm实例，构造函数中会执行this._init(options)进行初始化
new Vue(options) {
    this._init(options)
}
```

```
Vue.prototype._init = function (options?: Object) {
    const vm: Component = this
    // vm实例上定义_uid属性，相当于给每个vm实例定义一个唯一id
    vm._uid = uid++
    vm._isVue = true
    // 合并options
    if (options && options._isComponent) {
      initInternalComponent(vm, options)
    } else {
      vm.$options = mergeOptions(
        resolveConstructorOptions(vm.constructor),
        options || {},
        vm
      )
    }
    vm._self = vm
    //确定组件实例之间的父子关系
    initLifecycle(vm)
    //初始化组件中函数对象，里面存放组件实例中的自定义函数
    initEvents(vm)
    //为渲染vnode提供基础api
    initRender(vm)
    //执行beforeCreate生命周期钩子，使用vuex时使用到了这个钩子
    callHook(vm, 'beforeCreate')
    //组件之间进行通信时，使用inject和provide，这里先初始化inject
    initInjections(vm) // resolve injections before data/props
    //初始化组件中定义的data、props、methods、watch、computed，利用Object.defineProperties重写get/set方法
    initState(vm)
    //组件通信时使用，初始化provide对象
    initProvide(vm) // resolve provide after data/props
    //执行created钩子，此时vm上的数据可以获取到，可以进行初始化的逻辑处理，比如请求服务端接口
    callHook(vm, 'created')
    //将vm实例进行挂载
    if (vm.$options.el) {
      vm.$mount(vm.$options.el)
    }
  }
}
```


### 更新渲染
