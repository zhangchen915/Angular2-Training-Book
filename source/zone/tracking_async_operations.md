## 跟踪异步操作

我们已经看到了如何进入和退出 zones，现在让我们看看我们如何跟踪跨异步操作的 zones 。

```js
let rootZone = Zone.current;
let zoneA = rootZone.fork({name: 'A'});
expect(Zone.current).toBe(rootZone);
// setTimeout调用时的zone是rootZone
expect(Error.captureStackTrace()).toEqual(rootLocation)
setTimeout(function timeoutCb1() {
    // 回调在rootZone中执行
    expect(Zone.current).toEqual(rootZone);
    expect(Error.captureStackTrace()).toEqual(rootLocationRestored)
}, 0);
zoneA.run(function run1() {
    expect(Zone.current).toEqual(zoneA);
    // setTimeout调用时的zone是zoneA
    expect(Error.captureStackTrace()).toEqual(zoneALocation)
    setTimeout(function timeoutCb2() {
        // 回调在zoneA中执行
        expect(Zone.current).toEqual(zoneA);
        expect(Error.captureStackTrace()).toEqual(zoneALocationRestored)
    }, 0);
});
```

```js
rootLocation:
  at <anonymous>()[rootZone]
rootLocationRestored:
  at timeoutCb1()[rootZone]
  at Zone.prototype.run()[<root> -> <root>]
zoneALocation:
  at run1()[zoneA]
  at Zone.prototype.run()[<root> -> zoneA]
  at <anonymous>()[rootZone]
zoneALocationRestored:
  at timeoutCb2()[zoneA]
  at Zone.prototype.run()[<root> -> zoneA]
```

**关键点：**

- 当调度异步工作时，回调函数将在与调用异步API时存在的相同 zone 中执行。 这允许跨越许多异步调用来跟踪 zone。

类似示例使用promises。（Promises有点不同，因为它们在回调中处理自己的异常）

```js
let rootZone = Zone.current;
// LibZone表示一些第三方库，开发人员无法控制。 这个zone仅用于说明目的。
// 在实践中，大多数第三方库不可能有这种细粒度zone控制。
let libZone = rootZone.fork({name: 'libZone'});
// Represents a zone of an application which the developer does
// control
let appZone = rootZone.fork({name: 'appZone'});
// In this Example we try to demonstrate the difference between
// resolving a promise and listening to a promise. Promise
// resolution could happen in a third party libZone.
let promise = libZone.run(() => {
  return new Promise((resolve, reject) => {
    expect(Zone.current).toBe(libZone);
    // The Promise can be resolved immediately or at some later
    // point in time as in this example.
    setTimeout(() => {
      expect(Zone.current).toBe(libZone);
      // Promise is resolved in libZone, but this does not affect
      // the promise listeners.
      resolve('OK');
    }, 500);
  });
});
appZone.run(() => {
  promise.then(() => {
    // Because the developer controls the in which zone .then()
    // executes, they will expect that the callback will execute
    // the same zone, in this case the appZone.
    expect(Zone.current).toBe(appZone);
  });
});
```
**关键点：**

- 对于Promise，在`.then()` 调用生效时 thenCallback 会在 zone 中被调用  。
  - 或者，我们可以为thenCallback使用不同的 zone ，例如Promise创建 zone 或Promise解析 zone 。 这两者都不是一个好的匹配，因为一个Promise可以在一个第三方库中创建和解析，它可以有自己的 zone 。 然后将所得到的承诺传递到在其自己的 zone 中运行的应用程序中。 如果应用程序在其自己的 zone 中注册`.then()`，那么它将期望它自己的 zone 传播。

    示例：调用`fetch()`返回一个promise。 内部`fetch()`可能使用自己的 zone 为自己的原因。 调用.then()的应用程序将期望应用程序 zone 。 （我们不希望`fetch()` zone 泄漏到我们的应用程序中。）

