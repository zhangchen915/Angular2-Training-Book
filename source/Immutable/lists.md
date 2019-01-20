#Immutable.List
`List`是JavaScript数组结构的不可变版本。
```js
let movies = Immutable.fromJS([ // again use fromJS for deep immutability
  {
    name: 'The Fellowship of the Ring',
    released: 2001,
    rating: 8.8
  },
  {
    name: 'The Two Towers',
    released: 2002,
    rating: 8.7
  }
]);

movies = movies.push(Immutable.Map({
    name: 'The Return of the King',
    released: 2003
}));

movies = movies.update(2, movie => movie.set('rating', 8.9)); // 0 based

movies = movies.zipWith(
  (movie, seriesNumber) => movie.set('episode', seriesNumber),
  Immutable.Range(1, movies.size + 1) // size property instead of length
);

console.log(movies);
/* writes
List [
  Map { "name": "The Fellowship of the Ring", "released": 2001, "rating": 8.8, "episode": 1 },
  Map { "name": "The Two Towers", "released": 2002, "rating": 8.7, "episode": 2 },
  Map { "name": "The Return of the King", "released": 2003, "rating": 8.9, "episode": 3 } ]
  */
```

这里我们再次使用`Immutable.fromJS`调用，因为我们有对象存储在数组中。 我们调用`push`来向列表中添加项目，就像我们在数组上调用它一样。 但是因为我们创建了一个新的副本，我们必须重新绑定变量。 当我们想更新特定索引中的项目时，我们有相同的 `set` 和 `update`调用。 我们还可以访问数组函数，如`map`，`reduce`还支持像我们这里使用的额外方法`zipWith` 。