## The case for ZoneDelegate

拦截 zone 事件是棘手的，因为 zone 是在运行时生成的，并且标准方法（如子类化和猴子补丁）只有在设计时已知父 zone时才起作用。 为了更好地演示这个问题，我们假设想在`run()`方法的前后进行拦截，这样我们可以测量执行时间和在控制台记录 zone。

这是一个破例来说明这个问题。

```js
// At design time it is not possible to know which zone will
// be the parent zone. As a consequence, a parent zone has to
// be passed into the constructor.
class TimingZone extends Zone {
  constructor(parent) { super(parent, 'timingZone');}
  // We would like to intercept the run, and so we overwrite it.
  run() {
    // Capture the start time
    var start = performance.now();
    // It may appear that calling super.run() is the right thing
    // to do at this point, but the super.run(), internally must
    // invoke parent.run. So super.run is just a red herring,
    // and we should rather understand what are the consequences
    // of parent.run(). See next example.
    super.run(...arguments);
    // Capture the end time
    var end = performance.now();
    // Print the duration, and the current zone.
    console.log(this.name, 'Duration:', end - start);
  }
}
// At design time it is not possible to know which zone will
// be the parent zone. As a consequence, a parent zone has to
// be passed into the constructor.
class LogZone extends Zone {
  constructor(parent) { super(parent, 'logZone');}
  run() {
    // log the zone name and 'enter'
    console.log(this.name, 'enter');
    // The issue with calling parent.run, is that it will cause the
    // current zone to be changed to the parent zone.
    // What we need is a way of calling the parent run hooks, without
    // changing the current zone.
    this.parent.run.apply(this, arguments);
    // log the zone name and 'leave'
    console.log(this.name, 'leave');
  }
}
```

让我们使用上述 zone 创建一个简单的例子。

```js
// Compose several zones.
let rootZone = Zone.current;
let timingZone = new TimingZone(rootZone);
let logZone = new LogZone(timingZone);
logZone.run(() => {
  console.log(Zone.current.name, 'Hello World!');
});
```

Here is what one would expect from the above example. Specifically the expectation is that the current zone inside the run block will be that of the logZone.

这里是从上面的例子打印的实际

```js
logZone enter
logZone Hello World;
logZone Duration: 0.123
logZone leave
```

## 为什么标准工具不起作用

拦截调用（super或父级）的标准方式不起作用的原因是父区域在设计时是未知的。 如果父区域在设计时间是已知的，那么我们可以这样写：

```js
class TimingZone extends RootZone {
  run() {
    …
    super.run(...arguments);
    …
  }
}
class LogZone extends TimingZone {
  run() {
    …
    super.run(...arguments);
    …
  }
}
```

如果我们可以在设计时决定区域层次结构，`super.run()`的调用将按预期工作。 然而，因为父进程在运行时之前是未知的，所以我们需要将更改区域的行为与调用父钩子的行为分开。 为了解决这个问题，钩子被指定为fork（）的一部分，钩子接收一个父ZoneDelagate（只处理钩子）而不是一个父区域（这将导致一个区域改变）。