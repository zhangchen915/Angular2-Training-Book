#展开/剩余运算符
展开运算符接受一些集合，例如`[]`或`{}`，并将它们应用于接受，分离的参数（如函数，[]和{}）的其他东西。
```
const add = (a, b) => a + b;
let args = [3, 5];
add(...args); // same as `add(args[0], args[1])`, or `add.apply(null, args)`
```
函数不是JavaScript中唯一使用逗号分隔列表的地方 - []现在可以轻松连接：
```
let cde = ['c', 'd', 'e'];
let scale = ['a', 'b', ...cde, 'f', 'g']; // ['a', 'b', 'c', 'd', 'e', 'f', 'g']
```
同样，对象文本可以做同样的事情：
```
let mapABC  = { a: 5, b: 6, c: 3};
let mapABCD = { ...mapABC, d: 7}; // { a: 5, b: 6, c: 3, d: 7 }
```
... rest参数共享休止符操作符的省略号语法，但是用于不同的目的。 ... rest参数用于访问传递给函数的可变数量的参数。 例如：
```
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
技术上，JavaScript已经在每个函数上设置了一个参数变量（除了箭头函数），但是参数有很多问题，其中之一就是它在技术上不是数组。
... rest参数实际上是数组。 另一个重要的区别是rest参数只包括在函数中没有特别命名的参数，如：
```
function print(a, b, c, ...more) {
  console.log(more[0]);
  console.log(arguments[0]);
}

print(1, 2, 3, 4, 5);
// 4
// 1
```