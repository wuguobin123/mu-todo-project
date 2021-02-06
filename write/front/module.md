### js模块化

#### 无模块化
通过script标签来引入依赖，但是引入时需要注意先后顺序，大多数情况下我们都使用默认的同步加载方式，如果js不依赖dom并且没有先后顺序要求，可以设置为异步（async与defer）
```
<script type="text/javascript" src="jQuery.js"></script>
<script type="text/javascript" src="jQuery-plugin.js"></script>
```
- 这样引入简单，但是复用性较差
- 容易造成全局变量的污染，也就是后边的全局变量有可能覆盖前边的（定义名字相同的情况下）
- 各个模块之间的依赖关系不明显

#### module.exports与require（commonjs）
适用于node端的模块化管理，它有四个环境变量为模块化提供支持，module、exports、require、global，实际开发中通过module.exports对外输出模块，通过require来加载模块。
module代表当前模块，它是一个对象，它的属性exports是对外的接口，加载某个模块，其实是加载的某个模块的module.exports属性。

```
//通过在module.exports添加属性或方法的方式来对外输出
//a.js文件
module.exports.name = 'xiaoming';
module.exports.sum = function(a, b) {
  return a + b;
}
//b.js文件
var obj = require("./a.js");
console.log(obj.name);
console.log(obj.sum(2, 3));

//通过直接给module.exports赋值来对外输出
//a.js
module.exports = function sum(a, b) {
  return a + b;
}
//b.js
var sum = require('./a.js');
console.log(sum(3, 5));
```
- 解决了全局污染和依赖关系不明显的问题
- 在运行时加载模块，可以用在条件判断中
- 同步的方式加载模块，在服务端会比较快，但是浏览器端就会影响性能
- 模块加载之后，后续再次引入相同模块会使用第一次加载模块的缓存，如果这时模块内部进行了修改，后续的使用是不会起作用的
- commonjs模块输出的是一个值的拷贝，也就是说一旦输出一个值，模块内部的变化就影响不到这个值
- 运行时加载，commonjs模块就是对象，在输入时先加载整个模块，生成一个对象，然后再从这个对象上读取方法
- 不用使用在浏览器端

#### AMD(Asynchronous Module Definition)
异步加载模块，并可以设置回调函数。
```
/*
  id: 模块名字
  dependencies: 当前定义的模块依赖的其他模块
  factory: 可以是回调函数，也可以是对象。如果是函数则只执行一次，如果是对象，则该对象为该模块的输出值
*/
define(id, dependencies, factory)

define(['./a', './b'], function(a, b) {  
  a.doSomething();
  b.doSomething();
  ...
})
```
- AMD推荐依赖前置，优先加载依赖，也就是相比CMD提前执行依赖模块

#### CMD(Common Module Definition)
CMD的使用，推荐传入一个参数factory，该参数会传入三个参数 require、exports 和 module。
```
define(factory(require, exports, module) {
  var a = require('./a.js');
  a.doSomething();
  var b= require('./b.js');
  b.doSomething();
  ...
})
```
- CMD推荐依赖就近，也就是依赖哪些模块，在使用时引入即可。延迟执行

#### UMD(Universal Module Definition)
通用模块规范，适用于浏览器端也适用于node端
```
//jQuery的通用化demo
(function (factory) {
    //浏览器使用amd模块规范
    if (typeof define === 'function' && define.amd) {
        define(['jquery'], factory);
    //服务端使用commonjs规范    
    } else if (typeof module === 'object' && module.exports) {
        module.exports = function( root, jQuery ) {
            if ( jQuery === undefined ) {
                if ( typeof window !== 'undefined' ) {
                    jQuery = require('jquery');
                }
                else {
                    jQuery = require('jquery')(root);
                }
            }
            factory(jQuery);
            return jQuery;
        };    
    } else {
        // Browser globals
        factory(jQuery);
    }
}(function ($) {
    $.fn.jqueryPlugin = function () { return true; };
}));
```

#### export与import（es6模块化）
es6模块化是当前推崇的模块化的方式，通过export输出模块，使用import引入模块。
```
//a.js
export function sum(a, b) {
  return a + b;
}
//b.js
import { sum } from './a.js';
console.log(sum(3, 5));

//export default的用法,引入时不需要大括号{}。在一个模块中只能有一个export default
//a.js
export default function sum(a, b) {
  return a + b;
}
//b.js
import sum from './a.js';
console.log(sum(3, 5));

//a.js
function sum(a, b) {
  return a + b;
}
function minus(a, b) {
  return a - b;
}
export default { sum, minus };

//b.js
import cal from './a.js';
console.log(cal.sum(3, 5));
console.log(cal.minus(5, 3));
```
- 编译时加载，ES6模块不是对象，而是通过export命令显式输出制定代码，通过import指定加载某个输出值，而不是加载整个模块，这种加载成为“编译时加载”
- ES6输出的是值的引用，js引擎对脚本进行静态分析时，遇到模块加载命令import时，会生成一个只读引用，等到脚本真正执行时，再根据这个引用找到真正的模块，加载对应的值
- ES6模块是动态引用，并不会缓存值，模块中的值绑定其所在的模块



