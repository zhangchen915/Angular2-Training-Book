#什么是依赖注入
所以依赖注入使程序员的生活更容易，但它真正做什么？

考虑下面的代码：

```typescript
class Hamburger {
  private bun: Bun;
  private patty: Patty;
  private toppings: Toppings;
  constructor() {
    this.bun = new Bun('withSesameSeeds');
    this.patty = new Patty('beef');
    this.toppings = new Toppings(['lettuce', 'pickle', 'tomato']);
  }
}
```
上面的代码是一个表示一个汉堡包类。 该类假设汉堡包括Bun, Patty 和 Toppings。 该类还负责制作Bun, Patty 和 Toppings。 这是一件坏事。 如果需要素食汉堡怎么办？ 一个天真的方法可能是：
```typescript
class VeggieHamburger {
  private bun: Bun;
  private patty: Patty;
  private toppings: Toppings;
  constructor() {
    this.bun = new Bun('withSesameSeeds');
    this.patty = new Patty('tofu');
    this.toppings = new Toppings(['lettuce', 'pickle', 'tomato']);
  }
}
```
有，问题解决了吗？ 但是如果我们需要一个无麸质汉堡包呢？ 如果我们想要不同的Toppings...如果是更通用的东西如：
```typescript
class Hamburger {
  private bun: Bun;
  private patty: Patty;
  private toppings: Toppings;
  constructor(bunType: string, pattyType: string, toppings: string[]) {
    this.bun = new Bun(bunType);
    this.patty = new Patty(pattyType);
    this.toppings = new Toppings(toppings);
  }
}
```
好吧，这有点不同，它在某些方面更灵活，但它仍然相当脆弱。 如果Patty构造函数改变以允许新的功能会发生什么？ 整个汉堡包类必须更新。 事实上，任何时候任何这些在汉堡的构造函数中使用的构造函数都会改变，汉堡包也必须改变。

此外，测试期间会发生什么？ 如何有效地mocked Bun, Patty 和 Toppings？

考虑这些关注，类可以重写为：

```typescript
class Hamburger {
  private bun: Bun;
  private patty: Patty;
  private toppings: Toppings;
  constructor(bun: Bun, patty: Patty, toppings: Toppings) {
    this.bun = bun;
    this.patty = patty;
    this.toppings = toppings;
  }
}
```
现在当汉堡实例化时，它不需要知道关于它的 Bun, Patty 和 Toppings。 这些元素的构建已经被移出类了。 这种模式非常常见以至于TypeScript允许将他它简写成如下形式：
```typescript
class Hamburger {
  constructor(private bun: Bun, private patty: Patty, 
    private toppings: Toppings) {}
}
```
汉堡包类现在更简单，更容易测试。 这种将依赖项提供给Hamburger的模型是基本的依赖注入。

但是还是有一个问题。 如何管理Bun，Patty和Toppings的实例化？

这是依赖注入作为框架可以使程序员受益的地方，它是Angular 2提供的依赖注入系统。