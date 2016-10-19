# **Classes**

类是在ES6一个新功能，用来描述对象的蓝图，使ECMAScript中的原型继承模型的功能更像是一个传统的基于类的语言。为什么说是“更像”，因为类实际上是个特殊的函数，定义类有两种，分别是：类声明和类表达式，这一点和函数声明一样，但是类的声明不存在变量提升，所以我们必须先声明类才能使用。

## 类的方法

1. 构造器方法
  构造器方法是一个特殊的类方法，其用于创建和初始化对象。一个类只能拥有一个名为 `constructor` 的方法，如果没有显式定义，一个空的`constructor`方法会被默认添加。

2. 静态方法 
  `static` 关键字用来定义类的静态方法。静态方法是指那些不需要对类进行实例化，使用类名就可以直接访问的方法，需要注意的是静态方法不能被实例化的对象调用。静态方法经常用来作为工具函数。

3. 原型方法
  ECMAScript6开始，引入了一种更简短的在对象初始器中定义方法的语法，这是一种把方法名直接赋给函数的简写方式。


```js
//ES5
var obj = {
    foo: function() {},
    bar: function() {}
};

//ES6
var obj = {
  foo() {},
  bar() {}
};
```

## 类的继承

1.使用 `extends` 创建子类

```js
class Animal { 
  constructor(name) {
    this.name = name;
  }

  speak() {
    console.log(this.name + ' makes a noise.');
  }
}

class Dog extends Animal {
  speak() {
    console.log(this.name + ' barks.');
  }
}

var d = new Dog('Mitzie');
d.speak();  // 'Mitzie barks.'
```
**注意：**子类必须在constructor方法中调用super方法，否则新建实例时会报错。这是因为子类没有自己的this对象，而是继承父类的this对象，然后对其进行加工。如果不调用super方法，子类就得不到this对象。 

2.使用 `super` 引用父类

`super` 关键字可以用来调用其父类的构造器或者类方法

* 作为函数调用时（即super\(...args\)），super代表父类的构造函数。
* 作为对象调用时（即super.prop或super.method\(\)），super代表父类。注意，此时super即可以引用父类实例的属性和方法，也可以引用父类的静态方法。

