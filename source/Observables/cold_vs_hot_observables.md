# “冷” vs “热” Observable

`Observables` 可以分为两大类：“冷”和“热” `Observables` 。让我们从一个“冷” `Observable`开始。

```javascript
const obsv = new Observable(observer => {

  setTimeout(() => {
    observer.next(1);
  }, 1000);

  setTimeout(() => {
    observer.next(2);
  }, 2000);

  setTimeout(() => {
    observer.next(3);
  }, 3000);

  setTimeout(() => {
    observer.next(4);
  }, 4000);

});

// Subscription A
setTimeout(() => {
  obsv.subscribe(value => console.log(value));
}, 0);

// Subscription B
setTimeout(() => {
  obsv.subscribe(value => console.log(`>>>> ${value}`));
}, 2500);
```

[View Example](http://jsbin.com/felanu/46/edit?js,console)

在上述情况下，用户B在用户A之后2000ms订阅。然而，用户B开始获得像用户A的值只是时间偏移的值。 这种行为被称为冷`Observables`。 一个有用的类比是观看预录制的视频，例如在Netflix上。 您按下Play键，电影从头开始播放。 别人可以在25分钟后在自己的家里开始播放同一部电影。

另一方面，还有一个热`Observables`，这更像是一个现场表演。 你从一开始就参加一个现场乐队表演，但有些人可能迟到25分钟的节目。 乐队不会从头开始播放，后来者必须从那里开始观看表演。

我们已经遇到了这两种`Observables`。 上面的例子是一个冷的`Observables`，而在我们的文本字段输入上使用`valueChanges`的例子是一个热的`Observables`。

### Cold Observables 转换到 Hot Observables

在RxJS API中的方法是`publish`方法。 此方法接受一个冷`Observable`作为其来源，并返回一个`ConnectableObservable`的实例。 在这种情况下，我们必须显式调用`connect`在我们的热`Observable`开始广播值给其订阅者。

```javascript
const obsv = new Observable(observer => {

  setTimeout(() => {
    observer.next(1);
  }, 1000);

  setTimeout(() => {
    observer.next(2);
  }, 2000);

  setTimeout(() => {
    observer.next(3);
  }, 3000);

  setTimeout(() => {
    observer.next(4);
  }, 4000);

}).publish();

obsv.connect();

// Subscription A
setTimeout(() => {
  obsv.subscribe(value => console.log(value));
}, 0);

// Subscription B
setTimeout(() => {
  obsv.subscribe(value => console.log(`      ${value}`));
}, 2500);
```

[View Example](http://jsbin.com/fewotud/3/edit?js,console)

在上面的情况下，现场演出开始于1000ms，用户A在0秒到达音乐厅并找了一个好位置，用户B在2500ms处达到并错过了一首歌曲。
另一个有用的方法使用hot `Observables`而不是`connect`是`refCount` 。 这是一种自动连接方法，只要有多个用户，就会立即开始广播。 类似地，如果订阅者的数量到0，它将停止; 换句话说，如果观众中的每个人走出来，演出都会停止。