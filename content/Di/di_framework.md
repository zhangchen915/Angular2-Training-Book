#依赖注入框架
所以有一个新的汉堡包类，很容易测试，但它目前尴尬与合作。 实例化汉堡包需要：
```ts
const hamburger = new Hamburger(new Bun(), new Patty('beef'), new Toppings([]));
```
