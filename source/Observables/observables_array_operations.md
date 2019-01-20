# Observables 数组操作

In addition to simply iterating over an asynchronous collection, we can perform other operations such as filter or map and many more as defined in the RxJS API. This is what bridges an `Observable` with the iterable pattern, and lets us conceptualize them as collections.

Let's expand our example and do something a little more with our stream:

除了简单地遍历异步集合之外，我们还可以执行其他操作，例如过滤或映射，以及RxJS API中定义的更多操作。 这是一个`Observable`与可迭代模式的桥梁，让我们将它们概念化为集合。

让我们扩展我们的示例，并使用我们的流做一些事情：

```javascript
export class MyApp {
  private doctors = [];

  constructor(http: Http) {
    http.get('http://jsonplaceholder.typicode.com/users/')
        .flatMap((response) => response.json())
        .filter((person) => person.id > 5)
        .map((person) => "Dr. " + person.name)
        .subscribe((data) => {
          this.doctors.push(data);
        });
  }
}
```

[View Example](http://plnkr.co/edit/a0JuHC?p=preview)

这里有两个真正有用的数组操作 - `map` 和`filter`。 这些做什么呢？

- `map`将创建一个新的数组，其结果是在该数组中的每个元素上调用一个提供的函数。 在这个例子中，我们使用它来创建一个新的结果集，通过迭代每个项目，并追加“Dr.” 缩写在每个用户的名字前面。 现在我们数组中的每个对象都有“Dr.” 前面加上其name属性的值。
- `filter` 将创建一个新数组，其中所有元素都通过由提供的函数实现的测试。 在这里，我们使用它来创建一个新的结果集，排除任何id属性小于6的用户。

现在，当我们的subscribe回调被调用时，它接收的数据将是一个JSON对象的列表，其id属性大于或等于6，其名称属性已添加到“Dr .”.

请注意链接函数样式，以及TypeScript附带的可选静态类型，我们在本示例中使用。 最重要的是函数比如`filter`返回一个`Observable`，就像`Observable`中的其他`Observable`，类似`promise` 。为了在链接序列中使用`map` 和`filter` ，我们使用`flatMap`展平了我们的`Observable`的结果。 由于`filter`接受一个`Observable`而不是一个数组，我们必须将我们的JSON对象数组从`data.json()`转换为`Observable`流。 这是通过`flatMap`完成的。

你可以在Observable`中使用很多其他的数组操作; 在 [RxJS API](https://github.com/Reactive-Extensions/RxJS)中查找它们。

[rxmarbles.com](http://rxmarbles.com/) 是一个有用的资源，有助于你了解`operations`的工作原理。