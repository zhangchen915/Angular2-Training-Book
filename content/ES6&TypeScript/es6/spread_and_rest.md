# 扩展语句和剩余参数

在以下情况下，扩展语句允许表达式就地扩展：

1. 数组
2. 函数调用
3. 多变量解构

剩余参数与扩展语句相反，它将不定数量的逗号分隔的表达式收集到数组中。

# 扩展语句

```javascript
const add = (a, b) => a + b;
let args = [3, 5];
add(...args); // same as `add(args[0], args[1])`, or `add.apply(null, args)`
```
函数不是JavaScript中唯一使用逗号分隔列表的地方 - []现在可以轻松连接：
```javascript
let cde = ['c', 'd', 'e'];
let scale = ['a', 'b', ...cde, 'f', 'g']; // ['a', 'b', 'c', 'd', 'e', 'f', 'g']
```
同样，对象字面量可以做同样的事情：

```javascript
let mapABC  = { a: 5, b: 6, c: 3};
let mapABCD = { ...mapABC, d: 7}; // { a: 5, b: 6, c: 3, d: 7 }
```
# 剩余参数

剩余参数共享扩展语句的省略号语法，但目的不同。剩余参数用于访问传递给函数不确定数量的参数。 例如：

```javascript
function addSimple(a, b) {
  return a + b;
}

function add(...numbers) {
  return numbers[0] + numbers[1];
}

addSimple(3, 2);  // 5
add(3, 2);        // 5

// or in es6 style:
const addEs6 = (...numbers) => numbers.reduce((p, c) => p + c, 0);

addEs6(1, 2, 3);  // 6
```
技术上，JavaScript已经在每个函数上设置了一个`arguments` 变量（除了箭头函数），但是`arguments` 有很多问题，其中之一就是它在技术上不是数组。

剩余参数实际上是数组，它提供对`map`，`filter`，`reduce`等方法的访问。另一个重要的区别是剩余参数只包括在函数中没有特别命名的参数，如：

```javascript
function print(a, b, c, ...more) {
  console.log(more[0]);
  console.log(arguments[0]);
}

print(1, 2, 3, 4, 5);
// 4
// 1
```
*注意: 通常扩展语句和剩余参数被引用为Spread和Rest运算符，但它们不是根据ECMAScript规范的运算符。一些参考 [MDN-Spread Syntax](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Operators/Spread_operator), [MDN-Rest Parameters](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/rest_parameters), [ECMAScript Spec - Spread Syntax](http://www.ecma-international.org/ecma-262/6.0/#sec-array-initializer), [ECMAScript Spec - Rest Parameters](http://www.ecma-international.org/ecma-262/6.0/#sec-function-definitions)*