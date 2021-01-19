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

```
export function initLifecycle (vm: Component) {
  const options = vm.$options
  // 获取当前组件实例父组件实例
  let parent = options.parent
  if (parent && !options.abstract) {
    while (parent.$options.abstract && parent.$parent) {
      parent = parent.$parent
    }
    //将当前组件实例放到父实例的$children属性中
    parent.$children.push(vm)
  }
  //为当前组件实例添加属性$parent
  vm.$parent = parent
  //为当前组件实例添加属性$root
  vm.$root = parent ? parent.$root : vm
  //初始化其他属性  
  vm.$children = []
  vm.$refs = {}
  vm._watcher = null
  vm._inactive = null
  vm._directInactive = false
  vm._isMounted = false
  vm._isDestroyed = false
  vm._isBeingDestroyed = false
}
```

```
export function initEvents (vm: Component) {
  //当前组件实例添加_events属性，赋值空对象  
  vm._events = Object.create(null)
  vm._hasHookEvent = false
  // init parent attached events
  const listeners = vm.$options._parentListeners
  if (listeners) {
    updateComponentListeners(vm, listeners)
  }
}
```

```
//Vue.prototype定义函数相关的属性
export function eventsMixin (Vue: Class<Component>) {
  const hookRE = /^hook:/
  //添加函数到vm的_events中
  Vue.prototype.$on = function (event: string | Array<string>, fn: Function): Component {
    const vm: Component = this
    if (Array.isArray(event)) {
      for (let i = 0, l = event.length; i < l; i++) {
        vm.$on(event[i], fn)
      }
    } else {
      //将函数名和函数体添加到vm._events对象中去  
      (vm._events[event] || (vm._events[event] = [])).push(fn)
      // optimize hook:event cost by using a boolean flag marked at registration
      // instead of a hash lookup
      if (hookRE.test(event)) {
        vm._hasHookEvent = true
      }
    }
    return vm
  }

  //绑定函数，只执行一次，然后解绑  
  Vue.prototype.$once = function (event: string, fn: Function): Component {
    const vm: Component = this
    function on () {
      //解绑之后，执行fn  
      vm.$off(event, on)
      fn.apply(vm, arguments)
    }
    //将fn放在函数对象on上（toto：确定这里如此做的目的）
    on.fn = fn
    vm.$on(event, on)
    return vm
  }

  //解绑组件实例vm上的事件
  Vue.prototype.$off = function (event?: string | Array<string>, fn?: Function): Component {
    const vm: Component = this
    // 如果没有传参数，则解绑vm实例上的所有绑定的函数
    if (!arguments.length) {
      vm._events = Object.create(null)
      return vm
    }
    // 只传事件名称，则需要解绑该名称对应的所有的事件（一个事件名对应事件列表）
    if (Array.isArray(event)) {
      for (let i = 0, l = event.length; i < l; i++) {
        vm.$off(event[i], fn)
      }
      return vm
    }
    // specific event
    const cbs = vm._events[event]
    if (!cbs) {
      return vm
    }
    if (!fn) {
      vm._events[event] = null
      return vm
    }
    // 传入事件名和具体的事件，则遍历事件名对应的所有事件列表，删除对应的具体时间即可
    let cb
    let i = cbs.length
    while (i--) {
      cb = cbs[i]
      if (cb === fn || cb.fn === fn) {
        cbs.splice(i, 1)
        break
      }
    }
    return vm
  }

  //触发某个事件名对应的事件列表  
  Vue.prototype.$emit = function (event: string): Component {
    const vm: Component = this
    let cbs = vm._events[event]
    if (cbs) {
      cbs = cbs.length > 1 ? toArray(cbs) : cbs
      const args = toArray(arguments, 1)
      const info = `event handler for "${event}"`
      for (let i = 0, l = cbs.length; i < l; i++) {
        invokeWithErrorHandling(cbs[i], vm, args, vm, info)
      }
    }
    return vm
  }
}

//具体执行vm实例中_events中对应事件名的绑定事件
export function invokeWithErrorHandling (
  handler: Function,
  context: any,
  args: null | any[],
  vm: any,
  info: string
) {
  let res
  try {
    res = args ? handler.apply(context, args) : handler.call(context)
  } catch (e) {
    handleError(e, vm, info)
  }
  return res
}
```

```
let target: any
//这里的target在具体使用时赋值为vm，也就是在vm实例上添加函数事件
function add (event, fn) {
  target.$on(event, fn)
}

//在vm实例上解绑函数实例
function remove (event, fn) {
  target.$off(event, fn)
}

//执行一次之后进行解绑
function createOnceHandler (event, fn) {
  const _target = target
  return function onceHandler () {
    const res = fn.apply(null, arguments)
    if (res !== null) {
      _target.$off(event, onceHandler)
    }
  }
}

//更新组件的绑定函数
export function updateComponentListeners (
  vm: Component,
  listeners: Object,
  oldListeners: ?Object
) {
  target = vm
  updateListeners(listeners, oldListeners || {}, add, remove, createOnceHandler, vm)
  target = undefined
}
```

```
//判断变量是否存在
export function isUndef (v: any): boolean %checks {
  return v === undefined || v === null
}

//利用闭包缓存已经执行过的函数的结果
export function cached<F: Function> (fn: F): F {
  const cache = Object.create(null)
  return (function cachedFn (str: string) {
    const hit = cache[str]
    return hit || (cache[str] = fn(str))
  }: any)
}

//获取缓存闭包的返回结果（结果是函数）
const normalizeEvent = cached((name: string) => {
  const passive = name.charAt(0) === '&'
  //如果名字首字符是‘&’则取第一个字符之后的内容
  name = passive ? name.slice(1) : name
  const once = name.charAt(0) === '~' // Prefixed last, checked first
  //如果名字首字符是‘~’则取第一个字符之后的内容
  name = once ? name.slice(1) : name
  const capture = name.charAt(0) === '!'
  //如果名字首字符是‘!’则取第一个字符之后的内容
  name = capture ? name.slice(1) : name
  return {
    name,
    once,
    capture,
    passive
  }
})

export function updateListeners (
  on: Object,
  oldOn: Object,
  add: Function,
  remove: Function,
  createOnceHandler: Function,
  vm: Component
) {
  let name, def, cur, old, event
  for (name in on) {
    def = cur = on[name]
    old = oldOn[name]
    event = normalizeEvent(name)
    //如果
    if (isUndef(cur)) {
      process.env.NODE_ENV !== 'production' && warn(
        `Invalid handler for event "${event.name}": got ` + String(cur),
        vm
      )
    } else if (isUndef(old)) {
      if (isUndef(cur.fns)) {
        cur = on[name] = createFnInvoker(cur, vm)
      }
      if (isTrue(event.once)) {
        cur = on[name] = createOnceHandler(event.name, cur, event.capture)
      }
      //将函数添加到vm._events属性上
      add(event.name, cur, event.capture, event.passive, event.params)
    } else if (cur !== old) {
      old.fns = cur
      on[name] = old
    }
  }
  //解绑之前的函数
  for (name in oldOn) {
    if (isUndef(on[name])) {
      event = normalizeEvent(name)
      remove(event.name, oldOn[name], event.capture)
    }
  }
}
```


```
//初始化渲染相关的函数
export function initRender (vm: Component) {
  vm._vnode = null // the root of the child tree
  vm._staticTrees = null // v-once cached trees
  const options = vm.$options
  const parentVnode = vm.$vnode = options._parentVnode // the placeholder node in parent tree
  const renderContext = parentVnode && parentVnode.context
  vm.$slots = resolveSlots(options._renderChildren, renderContext)
  vm.$scopedSlots = emptyObject
  // bind the createElement fn to this instance
  // so that we get proper render context inside it.
  // args order: tag, data, children, normalizationType, alwaysNormalize
  // internal version is used by render functions compiled from templates
  vm._c = (a, b, c, d) => createElement(vm, a, b, c, d, false)
  // normalization is always applied for the public version, used in
  // user-written render functions.
  vm.$createElement = (a, b, c, d) => createElement(vm, a, b, c, d, true)

  // $attrs & $listeners are exposed for easier HOC creation.
  // they need to be reactive so that HOCs using them are always updated
  const parentData = parentVnode && parentVnode.data

  /* istanbul ignore else */
  if (process.env.NODE_ENV !== 'production') {
    defineReactive(vm, '$attrs', parentData && parentData.attrs || emptyObject, () => {
      !isUpdatingChildComponent && warn(`$attrs is readonly.`, vm)
    }, true)
    defineReactive(vm, '$listeners', options._parentListeners || emptyObject, () => {
      !isUpdatingChildComponent && warn(`$listeners is readonly.`, vm)
    }, true)
  } else {
    defineReactive(vm, '$attrs', parentData && parentData.attrs || emptyObject, null, true)
    defineReactive(vm, '$listeners', options._parentListeners || emptyObject, null, true)
  }
}
```

### 更新渲染
