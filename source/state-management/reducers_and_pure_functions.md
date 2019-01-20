# Reducers 和纯函数

Redux的核心概念之一是reducer。 reducer是一个带有签名 `(accumulator: T, item: U) => T`的函数。通过`Array.reduce`方法经常在JavaScript中使用Reducers ，该方法遍历每个数组的项目并累加一个值作为结果 。Reducers 应该是纯函数，意味着它们不会产生任何副作用。

reducer 的简单示例：

```typescript
let x = [1, 2, 3].reduce((sum, number) => sum + number, 0);
// x == 6
```

