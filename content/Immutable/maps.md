#Immutable.Map
Map是JavaScript对象结构的不可变版本。 由于JavaScript对象具有简洁的对象字面量语法，它通常用作键值存储，键为类型字符串。 此模式紧跟地图数据结构。 让我们重温前面的例子，但改用Immutable.Map。
```ts
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
不是直接将对象字面量绑定到movie1，而是将其作为参数传递给Immutable.Map。 这改变了我们如何与movie1的属性交互。
要获取属性的值，我们调用get方法，传递我们想要的属性名称，就像我们如何使用对象的字符串索引器。
要设置属性的值，我们调用set方法，传递属性名和新值。 注意，它不会改变现有的Map对象 - 它返回一个带有更新属性的新对象，因此我们必须将movie2变量重新绑定到新对象。