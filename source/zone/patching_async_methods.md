## 异步方法补丁

这里我们将看看如何通过异步回调传播zone的基本机制。 （注意：实际工作情况有点复杂，因为它们通过稍后讨论的任务调度机制进行调度，为了清楚起见，该示例进行了简化）。

```js
// 保存原始的setTimeout引用
let originalSetTimeout = window.setTimeout;
// Overwrite the API with a function which wraps callback in zone.
window.setTimeout = function(callback, delay) {
  // Invoke the original API but wrap the callback in zone.
  return originalSetTimeout(
    // Wrap the callback method
    Zone.current.wrap(callback),
    delay
  );
}
// Return a wrapped version of the callback which restores zone.
Zone.prototype.wrap[c] = function(callback) {
  // Capture the current zone
  let capturedZone = this;
  // Return a closure which executes the original closure in zone.
  return function() {
    // Invoke the original callback in the captured zone.
    return capturedZone.runGuarded(callback, this, arguments);
  };
};
```

**关键点：**

- Zones  的猴子补丁方法只修补一次。
- 进入/离开 zone 只需更改Zone.current的值。（不需要进一步的猴子补丁）
- Zone.prototype.wrap method provides convenience for wrapping callbacks. (The wrapped callback is executed through Zone.prototype.runGuarded())
- Zone.prototype.runGuarded() is just like Zone.prototype.run(), but with extra try-catch block for handling exceptions [described later](#h.8wa5ihid448h).



```JS
// Save the original reference to Promise.prototype.then.
let originalPromiseThen = Promise.prototype.then;
// Overwrite the API with function which wraps the arguments
// NOTE: this is simplified as actual API has more arguments.
Promise.prototype.then = function(callback) {
  // Capture the current zone
  let capturedZone = Zone.current;
  // Return a closure which executes the original closure in zone.
  function wrappedCallback() {
    return capturedZone.run(callback, this, arguments);
  };
  // Invoke the original callback in the captured zone.
  return originalPromiseThen.call(this, [wrappedCallback]);
};
```

**关键点：**

- Promises handle their own exceptions and so they can't use Zone.prototype.wrap(). (We could have separate API, but Promise is the exception to the rule, and so I did not feel justified creating its own API.)
- Promise API较多，在此示例中未显式显示。