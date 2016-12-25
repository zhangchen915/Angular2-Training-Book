# 回顾一下 `this`

在JavaScript类中，我们将使用`this`关键字来引用类的实例。例如，考虑这种情况：

```javascript
class Toppings {
  ...

  formatToppings() { /* implementation details */ }

  list() {
    return this.formatToppings(this.toppings);
  }
}
```

这里的`this`指的是`Toppings`类的一个实例。 只要使用点符号调用list方法，如`myToppings.list()`，则`this.formatToppings(this.toppings)`调用在类的实例上定义的`formatToppings()`方法。 这也将确保`formatToppings`内部，`this`指的是同一个实例。

但是，`this`也可以指其他事情。有两种基本情况你应该记住。

1. 方法调用：

   ```
     someObject.someMethod();

   ```

   这里，在`someMethod`内的`this`将引用`someObject，`这通常是你想要的。

2. 函数调用：

   ```
     someFunction();
   ```

   这里，在someFunction内的`this`可以指不同的东西，这取决于我们是否处于“strict”模式。 不使用“strict”模式，这是指调用`someFunction()`的上下文，这很少是你想要的。 在“严格”模式下，`this`将是未定义的。

[View Example](http://jsbin.com/vekawimihe/2/edit?js,console)

其中一个含义是，你不能轻易地从它的对象中分离一个方法。考虑这个例子：

```
  var log = console.log;
  log('Hello');

```

In many browsers this will give you an error. That's because `log` expects `this` to refer to `console`, but the reference was lost when the function was detached from `console`.

This can be fixed by setting `this` explicitly. One way to do this is by using `bind()` method, which allows you to specify the value to use for `this` inside the bound function.

在许多浏览器中，这将给你一个错误。 这是因为`log`期望`this`指的是`console` ，但是当函数从`console`分离时，引用丢失了。

这可以通过明确设置来固定。 一种方法是使用`bind()`方法，它允许您指定要用于绑定函数内的`this`的值。

```
  var log = console.log.bind(console);
  log('Hello');
```

你也可以使用`Function.call`和`Function.apply`实现相同的功能，但我们不在这里讨论。

另一个可能令人困惑的实例是匿名函数或在其他函数中声明的函数。 考虑以下：

```javascript
class ServerRequest {
   notify() {
     ...
   }
   fetch() {
     getFromServer(function callback(err, data) {
        this.notify(); // this is not going to work
     });
   }
}
```

在上面的情况下，`this`不会指向期望的对象：在“strict”模式下，它将是未定义的。这导致另一个ES6功能 - 箭头功能，将在下面覆盖。

