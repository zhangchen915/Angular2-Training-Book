#解构赋值
解构是一种无需编写大量代码就能从`{}`或`[]`中快速提取数据的方式。
从MDN借用，可以使用解构来转换以下内容：
```js
let foo = ['one', 'two', 'three'];

let one   = foo[0];
let two   = foo[1];
let three = foo[2];
```
转换成
```js
let foo = ['one', 'two', 'three'];
let [one, two, three] = foo;
console.log(one); // 'one'
```
这很有趣，但是起初可能很难看到应用场景。 ES6还支持对象解构，这可能使用途更明显：
```js
let myModule = {
  drawSquare: function drawSquare(length) { /* implementation */ },
  drawCircle: function drawCircle(radius) { /* implementation */ },
  drawText: function drawText(text) { /* implementation */ },
};

let {drawSquare, drawText} = myModule;

drawSquare(5);
drawText('hello');
```
解构也可以用于将对象传递到函数中，从而允许以简洁的方式从对象中提取特定属性。还可以为解构参数分配默认值，如果传入配置对象，这可能是一个有用的模式。
```
let jane = { firstName: 'Jane', lastName: 'Doe'};
let john = { firstName: 'John', lastName: 'Doe', middleName: 'Smith' }
function sayName({firstName, lastName, middleName = 'N/A'}) {
  console.log(`Hello ${firstName} ${middleName} ${lastName}`)  
}

sayName(jane) // -> Hello Jane N/A Doe
sayName(john) // -> Helo John Smith Doe
```
有许多更复杂的事情可以通过解构解决，MDN有一些很好的例子，包括嵌套对象解构和`for...in`运算符的动态解构。