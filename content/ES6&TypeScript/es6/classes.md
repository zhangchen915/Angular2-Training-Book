# 类

类是ES6中的一个新特性，用于描述对象的蓝图，使EcmaScript的原型继承模型函数更像一个传统的基于类的语言。

```javascript
class Hamburger {
  constructor() {
    // This is the constructor.
  }
  listToppings() {
    // This is a method.
  }
}
```

Traditional class-based languages often reserve the word `this` to reference the current (runtime) instance of the class. In Javascript `this` refers to the calling context and therefore can change to be something other than the object.

传统的基于类的语言通常保留`this`这个词来引用类的当前（运行时）实例。在 Javascript 中，`this`指的是调用上下文，因此可以改变为除对象之外的其他东西。

## 对象

对象是使用`new`运算符创建的类的实例。当使用点符号访问对象上的方法时，`this`将引用点左边的对象。

```javascript
let burger = new Hamburger();
burger.listToppings();
```

在上面的代码片段中，每当从Hamburger类中使用`this`时，它将引用对象汉堡`burger` 。

## 更改调用上下文

JavaScript代码可以选择在调用时使用以下方法之一将`this`提供给方法。

- Function.prototype.call(object [,arg, ...])
- Function.prototype.bind(object [,arg, ...])
- Function.prototype.apply(object [,argsArray])