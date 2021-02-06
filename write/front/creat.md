### js开发时几种使用方式

#### class定义

```
//类似于java的开发模式，借助于面向对象的思想，定义类，并且通过定义属性、静态函数、非静态函数的方式来具体描述类
class Animal {
  construtor(name, color) {
    //定义对象的属性
    this.name = name;
    this.color = color;
  }
  //相当于在函数的原型上定义方法
  sleep() {
    console.log('sleep');
  }
  //相当于在Animal的函数对象上定义方法
  static eat() {
    console.log('eat');
  }
}
```

#### prototype定义

```
//定义函数
function Vue() {

}
//函数原型上定义方法，通过new之后的实例来调用
Vue.prototype.init = function() {

}
//函数对象上定义属性，通过Vue.的形式来进行调用
Vue.util = {
  setCookie() {

  },
  getCookie() {

  }
}
```

#### 单例对象定义

```
//对外提供一个单例对象
const version = "1.0";
const app = {
  getVersion() {
    console.log(version);
  },
  getClientType() {

  }
}
```

#### 直接输出函数

```
export function sayHi() {
  console.log('hello world');
}

export function sayBye() {
  console.log('bye bye');
}
```