#Object.assign
Object.assign允许我们将一个对象的属性合并到另一个对象，用匹配的名称替换属性的值。我们可以使用它来复制对象的值，而不改变现有的值。
```ts
let movie1 = {
  name: 'Star Wars',
  episode: 7
};

let movie2 = Object.assign({}, movie1);

movie2.episode = 8;

console.log(movie1.episode); // writes 7
console.log(movie2.episode); // writes 8
```
你可以看到，虽然我们有一些复制对象的方法，但是我们没有使它不可变，因为我们可以将episode的属性设置为8.另外，在这种情况下我们如何修改episode属性？ 我们通过调用assign：
```ts
let movie1 = {
  name: 'Star Wars',
  episode: 7
};

let movie2 = Object.assign({}, movie1, { episode: 8 });

console.log(movie1.episode); // writes 7
console.log(movie2.episode); // writes 8
```