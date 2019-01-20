# 委托

在继承部分，我们研究了扩展类功能的一种方法，另一种方法是使用委托来扩展功能。 使用委托，一个对象将包含对不同对象的引用，它将切换执行功能的请求。
下面的代码显示如何使用委派与`Bird`类和`Penguin` 类。 `Penguin`类有一个对`Bird`类的引用，它将对自己的walk方法的调用委派给`Bird`的walk方法。

```javascript
// ES6
class Bird {
  constructor(weight, height) {
    this.weight = weight;
    this.height = height;
  }
  walk() {
    console.log('walk!');
  }
}

class Penguin {
  constructor(bird) {
    this.bird = bird;
  }
  walk() {
    this.bird.walk();
  }
  swim() {
    console.log('swim!');
  }
}

const bird = new Bird(...);
const penguin = new Penguin(bird);
penguin.walk(); //walk!
penguin.swim(); //swim!
```

关于“行为委派”的良好讨论可以在 [这里](https://github.com/getify/You-Dont-Know-JS/blob/master/this%20%26%20object%20prototypes/ch6.md)找到。