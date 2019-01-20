# 箭头函数

ES6 提供了一些处理 `this` 的新语法: "箭头函数"。箭头函数能使高阶函数更容易的工作。

新的“胖箭头”符号还可以用更简单的方式来定义匿名函数。

请看下面的例子：

```js
  items.forEach(function(x) {
    console.log(x);
    incrementedItems.push(x+1);
  });
```

可以通过“箭头函数”改写如下形式：

```javascript
  items.forEach((x) => {
    console.log(x);
    incrementedItems.push(x+1);
  });
```

计算一个表达式并返回值的函数可以被定义更简单：

```js
  incrementedItems = items.map((x) => x+1);
```

下面代码与上面几乎等价：

```js
  incrementedItems = items.map(function (x) {
    return x+1;
  });
```

但是箭头函数和一般函数有一个很重要的区别： 箭头函数不会产生自己作用域下的 `this`, `arguments`, `super`和`new.target`等对象。当`this` 在一个箭头函数内部使用时候，JavaScript 会使用函数所在上下文的 `this` 值。请看下面的例子：

```js
    class Toppings {
      constructor(toppings) {
        this.toppings = Array.isArray(toppings) ? toppings : [];
      }
      outputList() {
        this.toppings.forEach(function(topping, i) {
          console.log(topping, i + '/' + this.toppings.length);  // no this
        })
      }
    }
    
    var ctrl = new Toppings(['cheese', 'lettuce']);
    
    ctrl.outputList();
```

让我们在 [http://jsbin.com](http://jsbin.com/qakigoqulo/edit?js,console) 验证这段代码。不出所料，报错了，因为`this` 在匿名函数中的值是 undefined。

现在，让我们改变使用箭头函数的方式：

```javascript
    class Toppings {
      constructor(toppings) {
        this.toppings = Array.isArray(toppings) ? toppings : [];
      }
      outputList() {
        this.toppings
          .forEach((topping, i) => console
            .log(topping, i + '/' + this.toppings.length)  // `this` works! 
        )
      }
    }

    var ctrl = new Toppings(['cheese', 'lettuce']);
```

这个箭头函数内部的 `this` 指向的是实例变量.

_注意_ 箭头函数没有 `arguments` 对象 , 这会给开发者带来混乱. `super` 和 `new.target` 同样来自外部作用域。

###译注
* 箭头函数就是个简写形式的函数表达式；
* 它拥有词法作用域的this值（即不会新产生自己作用域下的this, arguments, super 和 new.target 等对象）；
* 箭头函数总是匿名的。
