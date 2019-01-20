#DI 框架

现在我们有一个花里胡哨的 `Hamburger` 类，它容易测试，但是目前工作起来很尴尬。实例化一个 `Hamburger` 需要：

```typescript
const hamburger = new Hamburger(new Bun(), new Patty('beef'), new Toppings([]));
```

创建一个汉堡包需要很多工作，现在所有不同的代码，使`Hamburger`必须了解如何将`Bun`, `Patty` 和`Toppings` 实例化。

处理这个新问题的一种方法可能是使用工厂函数：

```typescript
function makeHamburger() {
    const bun = new Bun();
    const patty = new Patty('beef');
    const toppings = new Toppings(['lettuce', 'tomato', 'pickles']);
    return new Hamburger(bun, patty, toppings);
}
```

这是一个改进，但是当需要创建更复杂的`Hamburger`时这个工厂将变得混乱。 工厂还负责知道如何创建四个不同的组件。 这对一个函数来说做得太多了。

这是一个依赖注入框架可以帮助。 DI框架具有`Injector`对象的概念。 注入器很像上面的工厂函数，但是更通用，功能强大。 而不是一个巨大的工厂函数，注入器有一个工厂，或*配方*（双关意图）的对象集合。 使用`Injector`，创建`Hamburger` 可以像以下一样简单：

```typescript
const injector = new Injector([Hamburger, Bun, Patty, Toppings]);
const burger = injector.get(Hamburger);
```

