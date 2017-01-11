# 回顾 Reducers 和纯函数

Redux的核心概念之一是reducer。 reducer是一个带签名的函数`(accumulator：T，item：U)=> T`。Reduce通常通过`Array.reduce`方法在JavaScript中使用，该方法遍历每个数组的项目并累加一个值作为结果 。 Reducers应该是纯函数，意味着它们不产生任何副作用。

reducer的一个简单示例是sum函数：

```js
let x = [1,2,3].reduce((value, state) => value + state, 0)
// x == 6
```