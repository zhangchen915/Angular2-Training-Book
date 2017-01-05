#Immutable.Map
`Map`是JavaScript对象结构的不可变版本。 由于JavaScript对象具有简洁的对象字面量语法，它通常用作键值存储，`key`为`string`类型。 此模式紧跟map数据结构。 让我们重温前面的例子，但改用`Immutable.Map`。

```js
import * as Immutable from 'immutable';

let movie1 = Immutable.Map<string, any>({
  name: 'Star Wars',
  episode: 7
});

let movie2 = movie1;

movie2 = movie2.set('episode', 8);

console.log(movie1.get('episode')); // writes 7
console.log(movie2.get('episode')); // writes 8
```
不是直接将对象字面量绑定到`movie1`，而是将其作为参数传递给`Immutable.Map`。 这改变了我们如何与`movie1`的属性交互。

要获取属性的值，我们调用`get`方法，传递我们想要的属性名称，就像我们如何使用对象的字符串索引器。

要设置属性的值，我们调用set`方法`，传递属性名和新值。 注意，它不会改变现有的Map对象 - 它返回一个带有更新属性的新对象，因此我们必须将`movie2`变量重新绑定到新对象。