#什么是不变性？
不可变性是一种设计模式，其中在实例化之后不能修改某些东西。 如果我们想改变那个东西的值，我们必须用新的值重新创建它。 一些JavaScript类型是不可变的，一些是可变的，意味着它们的值可以改变，而不必重新创建它。 让我们用一些例子来解释这个区别：
```typescript
let movie = {
  name: 'Star Wars',
  episode: 7
};

let myEp = movie.episode;

movie.episode = 8;

console.log(myEp); // outputs 7
```
正如你可以看到在这种情况下，虽然我们更改了`movie.episode`的值，`myEp`的值没有改变。 这是因为`movie.episode`的类型，`number`是不可变的。

```typescript
let movie1 = {
  name: 'Star Wars',
  episode: 7
};

let movie2 = movie1;

movie2.episode = 8;

console.log(movie1.episode); // outputs 8
```
然而，在这种情况下，更改一个对象上的`episode`的值也会更改另一个对象的值。 这是因为`movie1`和`movie2`是**Object** 类型，**Object** 是可变的。

在JavaScript内置类型中，以下是不可变的：

* Boolean
* Number
* String
* Symbol
* Null
* Undefined

以下是可变的：

* Object
* Array
* Function

String是一种不常见的情况，因为它可以使用`for...of`进行迭代，并像数组一样提供数字索引器，但会执行以下操作：
```typescript
let message = 'Hello world';
message[5] = '-';
console.log(message); // writes Hello world
```
> 这将在严格模式下抛出错误，并在非严格模式下静默失败。

