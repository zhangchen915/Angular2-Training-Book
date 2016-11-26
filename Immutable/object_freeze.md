#Object.freeze
Object.freeze允许我们禁用对象变动。
```ts
let movie1 = {
  name: 'Star Wars',
  episode: 7
};

let movie2 = Object.freeze(Object.assign({}, movie1));

movie2.episode = 8; // fails silently in non-strict mode,
                    // throws error in strict mode

console.log(movie1.episode); // writes 7
console.log(movie2.episode); // writes 7
```
然而，这种模式的一个问题是，我们的代码是会很冗长，以及使用对Object.freeze和Object.assign的所有样板调用读取和理解我们的数据实际上发生了什么会很困难。 我们需要一些更明智的接口来创建不可变数据和与其交互，这就是适用Immutable.js的地方。

> Object.freeze很慢，不应该与大数组一起使用。