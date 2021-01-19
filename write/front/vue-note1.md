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
//初始化渲染相关的函数,将渲染函数转成vnode
export function initRender (vm: Component) {
  ...
  // 将渲染函数转成vnode，该渲染函数是通过编译器将template编译处理之后的结果
  vm._c = (a, b, c, d) => createElement(vm, a, b, c, d, false)
  // 将渲染函数转成vnode，对用户定义的渲染函数进行处理
  vm.$createElement = (a, b, c, d) => createElement(vm, a, b, c, d, true)
  ...
}
```

```
export function initInjections (vm: Component) {
  //获取vm实例的inject对象中key对应的在父组件中provide的值，返回一个对象
  const result = resolveInject(vm.$options.inject, vm)
  if (result) {
    //调用该方法目的是在调用defineReactive时不进行依赖收集  
    toggleObserving(false)
    //将inject中定义的属性和对应值，绑定到当前的vm实例上去
    Object.keys(result).forEach(key => {
      if (process.env.NODE_ENV !== 'production') {
        defineReactive(vm, key, result[key], () => {
          warn(
            `Avoid mutating an injected value directly since the changes will be ` +
            `overwritten whenever the provided component re-renders. ` +
            `injection being mutated: "${key}"`,
            vm
          )
        })
      } else {
        //这里通过vm.key的方式就可以访问到inject中定义的属性值  
        defineReactive(vm, key, result[key])
      }
    })
    //重新恢复defineReactive执行时的依赖收集
    toggleObserving(true)
  }
}

//获取inject中对应的父组件中传过来的值，父组件是通过provide进行传值
export function resolveInject (inject: any, vm: Component): ?Object {
  if (inject) {
    const result = Object.create(null)
    const keys = hasSymbol
      ? Reflect.ownKeys(inject)
      : Object.keys(inject)

    for (let i = 0; i < keys.length; i++) {
      const key = keys[i]
      if (key === '__ob__') continue
      //在vm实例化时，会将组件中定义的inject进行格式化处理，返回inject: {app: { from: 'app' }}类似这样的数据格式
      const provideKey = inject[key].from
      let source = vm
      //不断查询当前组件实例的父级实例，得到provideKey对应的值，放到result中，如果找不到继续向上找
      while (source) {
        if (source._provided && hasOwn(source._provided, provideKey)) {
          result[key] = source._provided[provideKey]
          break
        }
        source = source.$parent
      }
    }
    return result
  }
}
```

```
//对组件实例vm中的data、props、watch、computed、methods进行处理
export function initState (vm: Component) {
  vm._watchers = []
  const opts = vm.$options
  if (opts.props) initProps(vm, opts.props)
  if (opts.methods) initMethods(vm, opts.methods)
  if (opts.data) {
    initData(vm)
  } else {
    observe(vm._data = {}, true /* asRootData */)
  }
  if (opts.computed) initComputed(vm, opts.computed)
  if (opts.watch && opts.watch !== nativeWatch) {
    initWatch(vm, opts.watch)
  }
}
```

```
//对props进行初始化
function initProps (vm: Component, propsOptions: Object) {
  const propsData = vm.$options.propsData || {}
  const props = vm._props = {}
  const keys = vm.$options._propKeys = []
  const isRoot = !vm.$parent
  // 如果组件实例不是根实例，设置defineReactive调用时不进行依赖收集
  if (!isRoot) {
    toggleObserving(false)
  }
  for (const key in propsOptions) {
    keys.push(key)
    const value = validateProp(key, propsOptions, propsData, vm)
    /* istanbul ignore else */
    if (process.env.NODE_ENV !== 'production') {
      const hyphenatedKey = hyphenate(key)
      if (isReservedAttribute(hyphenatedKey) ||
          config.isReservedAttr(hyphenatedKey)) {
        warn(
          `"${hyphenatedKey}" is a reserved attribute and cannot be used as component prop.`,
          vm
        )
      }
      defineReactive(props, key, value, () => {
        if (!isRoot && !isUpdatingChildComponent) {
          warn(
            `Avoid mutating a prop directly since the value will be ` +
            `overwritten whenever the parent component re-renders. ` +
            `Instead, use a data or computed property based on the prop's ` +
            `value. Prop being mutated: "${key}"`,
            vm
          )
        }
      })
    } else {
      //将属性设置到vm._props对象上，可以通过vm._props.key的形式来访问props中的属性  
      defineReactive(props, key, value)
    }
    //这里使用代理，可以通过vm.key的形式进行访问
    if (!(key in vm)) {
      proxy(vm, `_props`, key)
    }
  }
  toggleObserving(true)
}

//对vm实例上_props属性进行代理，可以通过vm.key的形式进行访问
export function proxy (target: Object, sourceKey: string, key: string) {
  sharedPropertyDefinition.get = function proxyGetter () {
    return this[sourceKey][key]
  }
  sharedPropertyDefinition.set = function proxySetter (val) {
    this[sourceKey][key] = val
  }
  Object.defineProperty(target, key, sharedPropertyDefinition)
}

//校验props属性，包括类型、默认值的
export function validateProp (
  key: string,
  propOptions: Object,
  propsData: Object,
  vm?: Component
): any {
  const prop = propOptions[key]
  //判断父组件中是否传入key，如果没有传值则下边会给该key赋一个值
  const absent = !hasOwn(propsData, key)
  let value = propsData[key]
  // 如果props中属性type的值为Boolean，则对value进行处理
  const booleanIndex = getTypeIndex(Boolean, prop.type)
  if (booleanIndex > -1) {
    //如果父组件没有传入，自身也没有设置default默认值，则设置为false
    if (absent && !hasOwn(prop, 'default')) {
      value = false
    } else if (value === '' || value === hyphenate(key)) {
      // only cast empty string / same name to boolean if
      // boolean has higher priority
      const stringIndex = getTypeIndex(String, prop.type)
      if (stringIndex < 0 || booleanIndex < stringIndex) {
        value = true
      }
    }
  }
  // 对默认值进行处理
  if (value === undefined) {
    value = getPropDefaultValue(vm, prop, key)
    const prevShouldObserve = shouldObserve
    toggleObserving(true)
    observe(value)
    toggleObserving(prevShouldObserve)
  }
  return value
}

//通过类型的toString()获取构造函数的字符串，然后使用正则获取字符串类型的具体类型值
function getType (fn) {
  const match = fn && fn.toString().match(/^\s*function (\w+)/)
  return match ? match[1] : ''
}

//对比两个字符串类型的类型是否相同
function isSameType (a, b) {
  return getType(a) === getType(b)
}

//如果期待类型和传入的值的类型一致返回0，否则返回-1
function getTypeIndex (type, expectedTypes): number {
  if (!Array.isArray(expectedTypes)) {
    return isSameType(expectedTypes, type) ? 0 : -1
  }
  for (let i = 0, len = expectedTypes.length; i < len; i++) {
    if (isSameType(expectedTypes[i], type)) {
      return i
    }
  }
  return -1
}

//判断某个对象是否包含某个属性
const hasOwnProperty = Object.prototype.hasOwnProperty
export function hasOwn (obj: Object | Array<*>, key: string): boolean {
  return hasOwnProperty.call(obj, key)
}

//驼峰式转化为-连接的字符串，例如：myNameIsXiaoMi=>my-name-is-xiao-mi
const hyphenateRE = /\B([A-Z])/g
export const hyphenate = cached((str: string): string => {
  return str.replace(hyphenateRE, '-$1').toLowerCase()
})
```
### 更新渲染
