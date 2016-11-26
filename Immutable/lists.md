#Immutable.List
`List`是JavaScript数组结构的不可变版本。
```ts
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
