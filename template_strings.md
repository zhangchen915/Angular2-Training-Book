# 模板字符串

在传统的JavaScript中，包含在匹配的“或”标记内的文本被认为是一个字符串。双引号或单引号内的文本只能在一行上，没有办法在这些字符串中插入数据，这导致了很多看起来像下面那样丑陋的串联代码：
```js
var name = 'Sam';
var age = 42;

console.log('hello my name is ' + name + ' I am ' + age + ' years old');
```
ES6引入了一种通过反引号（`）标记的新的字符串文字类型。 这些字符串文字可以包括换行符，并且有一个新的机制用于将变量插入字符串：
```js
var name = 'Sam';
var age = 42;

console.log(`hello my name is ${name}, and I am ${age} years old`);
```
这种字符串可以在各种各样的地方派上用场，前端Web开发是其中之一。

###译注
* 多行字符串
```
console.log(`string text line 1
string text line 2`);
// "string text line 1
// string text line 2"
```

* 嵌入表达式
```
console.log(`three is ${1 + 2} not ${2 * 1 + 2}.`);
// "three is 3 not 4."
```

* 带标签的模板字符串
```js
function tag(strings, ...values) {
      console.log(strings); // ["three is ", " not ", raw: Array[2]]
      console.log(values);  // [3, 4]
      return "Hello!";
}
tag`three is ${1 + 2} not ${2 * 1 + 2}`;
// "Hello!"
```
