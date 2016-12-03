#嵌套对象
Immutable.Map以浅的方式包装对象，这意味着如果你有一个对象的属性绑定到可变类型，那么这些属性可以被改变。
```ts
let movie = Immutable.Map({
    name: 'Star Wars',
    episode: 7,
    actors: [
        { name: 'Daisy Ridley', character: 'Rey'},
        { name: 'Harrison Ford', character: 'Han Solo' }
    ],
    mpaa: {
        rating: 'PG-13',
        reason: 'sci-fi action violence'
    }
});

movie.get('actors').pop();
movie.get('mpaa').rating = 'PG';

console.log(movie.toObject());

/* writes
{ name: 'Star Wars',
  episode: 7,
  actors: [ { name: 'Daisy Ridley', character: 'Rey' } ],
  mpaa: { rating: 'PG', reason: 'sci-fi action violence' } }
  */
```
要避免此问题，请改用Immutable.fromJS。
```ts
let movie = Immutable.fromJS({
    name: 'Star Wars',
    episode: 7,
    actors: [
        { name: 'Daisy Ridley', character: 'Rey'},
        { name: 'Harrison Ford', character: 'Han Solo' }
    ],
    mpaa: {
        rating: 'PG-13',
        reason: 'sci-fi action violence'
    }
});

movie.get('actors').pop();
movie.get('mpaa').rating = 'PG';

console.log(movie.toObject());

/* writes
{ name: 'Star Wars',
  episode: 7,
  actors: List [ Map { "name": "Daisy Ridley", "character": "Rey" }, Map { "name": "Harrison Ford", "character": "Han Solo" } ],
  mpaa: Map { "rating": "PG-13", "reason": "sci-fi action violence" } }
*/
```
所以，让我们说你想修改movie.mpaa.rating。 你可能会想这样做：movie = movie.get（'mpaa'）。set（'rating'，'PG'）。 然而，set将总是返回调用Map实例，在这种情况下返回映射绑定到mpaa键，而不是你想要的电影。 我们必须使用setIn方法来更新嵌套属性。
```ts
let movie = Immutable.fromJS({
    name: 'Star Wars',
    episode: 7,
    actors: [
        { name: 'Daisy Ridley', character: 'Rey'},
        { name: 'Harrison Ford', character: 'Han Solo' }
    ],
    mpaa: {
        rating: 'PG-13',
        reason: 'sci-fi action violence'
    }
});

movie = movie
  .update('actors', actors => actors.pop())
  .setIn(['mpaa', 'rating'], 'PG');

console.log(movie.toObject());

/* writes
{ name: 'Star Wars',
  episode: 7,
  actors: List [ Map { "name": "Daisy Ridley", "character": "Rey" } ],
  mpaa: Map { "rating": "PG", "reason": "sci-fi action violence" } }
*/
```
我们还添加了一个对Map.update的调用，与set不同，它接受一个函数作为第二个参数，而不是一个值。 此函数接受该键处的现有值，并且必须返回该键的新值。