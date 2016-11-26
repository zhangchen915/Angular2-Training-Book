#什么是不变性？
不可变性是一种设计模式，其中在实例化之后不能修改某些东西。 如果我们想改变那个东西的值，我们必须用新的值重新创建它。 一些JavaScript类型是不可变的，一些是可变的，意味着它们的值可以改变，而不必重新创建它。 让我们用一些例子来解释这个区别：
```ts
let movie = {
  name: 'Star Wars',
  episode: 7
};

let myEp = movie.episode;

movie.episode = 8;

console.log(myEp); // outputs 7
```
正如你可以看到在这种情况下，虽然我们更改了movie.episode的值，myEp的值没有改变。 这是因为movie.episode的类型，数字，是不可变的。
```ts
let movie1 = {
  name: 'Star Wars',
  episode: 7
};

let movie2 = movie1;

movie2.episode = 8;

console.log(movie1.episode); // outputs 8
```
然而，在这种情况下，更改一个对象上的episode的值也会更改另一个对象的值。 这是因为movie1和movie2是对象类型，对象是可变的。

在JavaScript内置类型中，以下是不可变的：

* Boolean
* Number
* String
* Symbol
* Null
* Undefined
 And the following are mutable:

* Object
* Array
* Function

String是一种不常见的情况，因为它可以使用`for...of`进行迭代，并像数组一样提供数字索引器，但会执行以下操作：
```ts
let message = 'Hello world';
message[5] = '-';
console.log(message); // writes Hello world
```
这将在严格模式下抛出错误，并在非严格模式下静默失败。

