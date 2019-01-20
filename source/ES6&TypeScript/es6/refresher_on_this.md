# 回顾一下 `this`

在JavaScript类中，我们将使用`this`关键字来引用类的实例。例如：

```javascript
class Toppings {
  ...

  formatToppings() { /* 实现细节 */ }

  list() {
    return this.formatToppings(this.toppings);
  }
}
```

这里的`this`指的是`Toppings`类的一个实例。 只要使用点号调用`list`方法，如`myToppings.list()`，则`this.formatToppings(this.toppings)`调用在类的实例上定义的`formatToppings()`方法。 这也将确保`formatToppings`内部，`this`指的是同一个实例。

但是，`this`也可以指其他东西。有两种基本情况你应该记住。

1. 方法调用：

   ```
     someObject.someMethod();
   ```

   这里，在`someMethod`内的`this`将引用`someObject`，这通常是你想要的。

2. 函数调用：

   ```
     someFunction();
   ```

   这里，在someFunction内的`this`可以指不同的东西，这取决于我们是否处于“strict”模式。 不使用“strict”模式，这是指调用`someFunction()`的上下文，这很少是你想要的。 在“strict”模式下，`this`将是未定义的。

[View Example](http://jsbin.com/vekawimihe/2/edit?js,console)

其中一个含义是，你不能轻易地从它的对象中分离一个方法。考虑这个例子：

```javascript
  var log = console.log;
  log('Hello');
```

在许多浏览器中这会报错。 这是因为`log`期望`this`指的是`console` ，但是当函数从`console`分离时，引用丢失了。

这可以通过明确设置来固定。 一种方法是使用`bind()`方法，它允许您指定要用于绑定函数内的`this`的值。

```javascript
  var log = console.log.bind(console);
  log('Hello');
```

你也可以使用`Function.call`和`Function.apply`实现相同的功能，但我们不在这里讨论。

另一个可能令人困惑的实例是匿名函数或在其他函数中声明的函数。 例如：

```javascript
class ServerRequest {
   notify() {
     ...
   }
   fetch() {
     getFromServer(function callback(err, data) {
        this.notify(); // 这不会生效
     });
   }
}
```

在上面的代码中，`this`不会指向期望的对象：在“strict”模式下，它将是未定义的。这引出另一个ES6特性 - 箭头函数，将在下面介绍。

