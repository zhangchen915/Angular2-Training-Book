# 继承

JavaScript的继承与其他语言中的继承的工作不同，这可能很混乱。 ES6类提供了一种语法糖，试图缓解使用ES5中存在的原型继承的问题。

为了说明这一点，我们创建一个动物园应用，其中创建鸟类。在经典继承中，我们定义一个基类，然后将其子类化以创建一个派生类。

## 子类化

下面的示例代码显示如何使用**extends**关键字从`Bird`派生`Penguin` 。还要注意在`Penguin`的子类构造函数中使用的**super**关键字，它用于将参数传递给基类`Bird`的构造函数。

`Bird`类定义了`Penguin`类继承的方法`walk`，并且可以被`Penguin`对象的实例使用。 同样，`Penguin`类定义了不能用于`Bird`对象的方法`swim`。 继承从上到下从基类到它的子类。

## 对象初始化

当使用`new`运算符创建对象时，调用类构造函数，在完全创建对象之前将调用它。 `consturctor`用于传递参数以初始化新创建的对象。

对象创建的顺序从它的基类开始，然后向下移动到任何子类。

```javascript
// 基类 : ES6
class Bird {
  constructor(weight, height) {
    this.weight = weight;
    this.height = height;
  }

  walk() {
    console.log('walk!');
  }
}

  // 子类
class Penguin extends Bird {
  constructor(weight, height) {
    super(weight, height);
  }

  swim() {
    console.log('swim!');
  }
}

// Penguin object
let penguin = new Penguin(...);
penguin.walk(); //walk!
penguin.swim(); //swim!
```

下面我们展示了在`class`引入JavaScript之前，原型继承是如何实现的。

```javascript
// JavaScript classical inheritance.

// Bird constructor
function Bird(weight, height) {
  this.weight = weight;
  this.height = height;
}

// Add method to Bird prototype.
Bird.prototype.walk = function() {
  console.log("walk!");
};

// Penguin constructor.
function Penguin(weight, height) {
   Bird.call(this, weight, height);
}

// Prototypal inheritance (Penguin is-a Bird).
Penguin.prototype = Object.create( Bird.prototype );
Penguin.prototype.constructor = Penguin;

// Add method to Penguin prototype.
Penguin.prototype.swim = function() {
  console.log("swim!");
};

// Create a Penguin object.
let penguin = new Penguin(50,10);

// Calls method on Bird, since it's not defined by Penguin.
penguin.walk(); // walk!

// Calls method on Penguin.
penguin.swim(); // swim!
```