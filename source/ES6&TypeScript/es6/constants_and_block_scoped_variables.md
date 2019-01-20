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

尽管引入块作用域，函数仍然是处理大多数循环的首选机制。

`let`工作类似`var`在其数据读/写的意义。 `let`在用于for循环时也很有用。 例如，不使用`let` ，以下示例将输出`5,5,5,5,5`：

```javascript
for(var x=0; x<5; x++) {
  setTimeout(()=>console.log(x), 0)
}
```

但是， 当我们使用 `let` 代替 `var` ，值回按我们希望的方式在限制的作用域内。

```javascript
for(let x=0; x<5; x++) {
  setTimeout(()=>console.log(x), 0)
}
```

此外，`const`是只读的。一旦`const`被分配，标识符不能被重新分配。 例如：

```javascript
const myName = 'pat';
let yourName = 'jo';

yourName = 'sam'; // assigns
myName = 'jan';   // error
```

只读性质可以用任何对象来演示：

```
const literal = {};

literal.attribute = 'test'; // fine
literal = []; // error;
```

但是有两种情况，const不会像你想象的那样工作。 

1. 一个const对象字面量
2. 对对象的const引用。

## Const对象字面量

```javascript
const person = {
  name: 'Tammy'
};

person.name = 'Pushpa'; // OK, name property changed.

person = null;          // "TypeError: Assignment to constant variable.
```

上面的示例演示了我们能够更改`person`对象的name属性，但是我们无法重置`person`的引用，因为它已标记为`const`

## 对象的Const引用

类似上面的代码是使用`const`引用，下面我们切换到使用`let`创建源文本（literal）对象。

```javascript
let person = {
  name: 'Tammy'
};

const p = person;

p.name = 'Pushpa'; // OK, name property changed.

p = null;          // "TypeError: Assignment to constant variable.
```

标记一个对象的引用为const不会让对象里的属性也为const。

[MDN:const](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/const)