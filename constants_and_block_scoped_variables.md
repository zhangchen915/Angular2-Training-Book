#常量和块作用域变量
ES6引入了块范围的概念。 块范围界定对来自其他语言（如C，Java或甚至PHP）的程序员来说是熟悉的。
在ES5 JavaScript和更早版本中，`var`的作用域是函数，它们可以在外部上下文中“看到”它们的函数。
```js
var five = 5;
var threeAlso = three; // error

function scope1() {
  var three = 3;
  var fiveAlso = five; // == 5
  var sevenAlso = seven; // error
}

function scope2() {
  var seven = 7;
  var fiveAlso = five; // == 5
  var threeAlso = three; // error
}
```
ES5中的函数本质上是可以在外面“看到”，但不能进入的容器。
在ES6中，var仍然以这种方式工作，使用函数作为容器，但有两种新的方法来声明变量：`const`和`let`。
`const`和`let`使用`{`和`}`块作为容器，故称为“块作用域”。 块作用域在循环期间非常有用。 考虑以下代码：
```
var i;
for (i = 0; i < 10; i += 1) {
  var j = i;
  let k = i;
}
console.log(j); // 9
console.log(k); // undefined
```
