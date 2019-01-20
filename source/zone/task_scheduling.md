## Task Scheduling

To track tasks, we have to revisit how setTimeout() is monkey patched to modify the way Zone's are tracked.

要跟踪任务，我们必须看下 setTimeout() 的猴子补丁如何修改被跟踪的 zone。

```js
// 保存setTimeout的原始引用
let originalSetTimeout = window.setTimeout;
// 使用在 zone 中包含回调的函数覆盖API。
window.setTimeout = function(callback, delay) {
  // 对当前 zone 使用scheduleTask API。
  Zone.current.scheduleMacroTask(
    // 调试信息
    'setTimeout',
    // 回调需要在当前 zone 执行。
    callback,
    // 可选数据，如任务是否重复。
    null,
    // 默认计划行为
    (task) => {
      return originalSetTimeout(
        // Use the task invoke method, so that the task can
        // call callback in the correct zone.
        task.invoke,
        // original delay information
        delay
      );
    });
}
```

使用示例：

```js
// Create a logging zone
let logZone = Zone.current.fork({
  onScheduleTask: function(parentZoneDelegate, currentZone,
                           targetZone, task) {
    // Print when async tasks are scheduled
    console.log('Schedule', task.source);
    return parentZoneDelegate.scheduleTask(targetZone, task);
  },
  onInvokeTask: function(parentZoneDelegate, currentZone,
                         targetZone, task, applyThis, applyArgs) {
    // Print when async tasks are invoked
    console.log('Invoke', task.source);
    return parentZoneDelegate.invokeTask(
             targetZone, applyThis, applyArgs);
  }
});
console.log('start');
logZone.run(() => {
  setTimeout(() => null, 0);
});
console.log('end');
```

输出结果

```
start
Schedule setTimeout
end
Invoke setTimeout
```

**关键点：**

- All APIs which schedule tasks use Zone.prototype.scheduleTask() instead of Zone.prototype.wrap().
- Tasks use Zone.prototype.runGuarded() for callback execution, hence they handle all of the errors.
- Tasks are intercepted using invokeTask() whereas zones are intercepted using invoke().