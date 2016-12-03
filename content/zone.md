# Zones

Zone.js提供了一种称为区域的机制，用于封装和拦截浏览器中的异步活动（例如setTimeout，promise）。

这些区域是执行上下文，允许Angular跟踪异步活动的开始和完成，并根据需要执行任务（例如，变化检测）。 Zone.js提供了一个全局区域，可以被分叉和扩展以进一步封装/隔离异步行为，Angular在其NgZone服务中通过创建一个fork并使用自己的行为扩展它。
NgZone服务为我们提供了一些Observable和方法来确定Angular的区域的状态，并在Angular的区域内外以不同的方式执行代码。

重要的是要知道Zone.js通过Monkey Patching浏览器中的常见方法和元素来完成这些各种拦截，例如。 setTimeout和HTMLElement.prototype.onclick。 这些拦截可能导致外部库和Angular之间的意外行为。 在某些情况下，可能优先在Angular的区域外执行第三方方法（见下文）。

## In The Zone

NgZone公开了一组Observable，允许我们确定Angular's区域的当前状态或稳定性。

* onUnstable - 当代码已经进入并正在Angular区域内执行时通知。
* onMicrotaskEmpty - 当没有更多的微任务被排队执行时通知。 Angular在内部订阅，表示它应该运行变化检测。
* onStable - 在最后一个onMicroTaskEmpty运行时通知，表示所有任务已完成，并且已发生变化检测。
* onError - 在发生错误时通知。 Angular在内部订阅，以将未捕获的错误发送到自己的错误处理程序，即您在控制台中看到的错误，前缀为“EXCEPTION：”。

订阅这些我们注入NgZone到我们的组件/服务/ etc。 并订阅公共Observables。

```typescript
import { Injectable, NgZone } from '@angular/core';

@Injectable()
export class OurZoneWatchingService() {
  constructor(private ngZone: NgZone) {
    this.ngZone.onStable.subscribe(this.onZoneStable);
    this.ngZone.onUnstable.subscribe(this.onZoneUnstable);  
    this.ngZone.onError.subscribe(this.onZoneError);
  }

  onZoneStable() {
    console.log('We are stable');
  }

  onZoneUnstable() {
    console.log('We are unstable');
  }

  onZoneError(error) {
    console.error('Error', error instanceof Error ? error.message : error.toString());
  }
}
```

订阅这些可以帮助您确定您的代码是否意外触发作为不影响应用程序状态的操作的结果的更改检测。

## Change Detection

因为从Angular的区域内执行的所有异步代码都可以触发变化检测，所以当不需要变化检测时，您可能更喜欢在Angular的区域之外执行一些代码。
为了在Angular的上下文之外运行代码，NgZone提供了一个名为runOutsideAngular的方法。 使用此方法，Angular的区域不会与您的代码交互，并且在全局区域变得稳定时不会接收事件。
在这个例子中，你将在日志中看到当代码运行在它的外部时，Angular的区域会发生什么。
你会注意到，在这两种情况下，点击按钮导致Angular区域变得不稳定，由于Zone.js修补和观看HTMLElement.prototype.onclick，但是在Angular的区域之外执行的setInterval不会影响它的稳定性，并且不会触发变化检测 。

## Debugging

Generally, exceptions thrown during a chain of asynchronous events will only include the current method in their stack trace.

With Zone.js tracking all of our asynchronous calls it can provide us a longer, more detailed, stack trace of the events and calls that occurred leading up to our exception.

To enable long stack traces in development, you should include the **long-stack-trace-zone** module in your code. It is a good idea not to include this in your production build but Angular will skip setting up longer stack traces when in production mode (`enableProdMode` from `@angular/core`).

Angular will take care of forking and extending its own zone to display more meaningful stack traces.

```
if (__PRODUCTION__) {
  enableProdMode();
} else {
  require('zone.js/dist/long-stack-trace-zone');
}

```

With the following code, we start by calling `startAsync` which triggers a chain of setTimeouts leading up to an uncaught error.

```
function startAsync() {
  setTimeout(stepOne, 100);
}

function stepOne() {
  setTimeout(stepTwo, 100);
}

function stepTwo() {
  throw new Error('Finished');
}

```

### Simple Stack trace

This is a typical stack trace that you would see in this scenario, without Zone, showing only the function where the unhandled exception occurred.

```
Uncaught Error: Finished(…)
  stepTwo @ debugging.html:28

```

### Detailed "Long" Stack trace

In the stack trace below, you can see the order of events that occurred within this asynchronous chain of function calls, '>>' has been added to point out our functions.

You'll notice this stack trace includes much more information, including Zone's own task management (e.g. `onScheduleTask`), as well as the time that elapsed between when the function was queued and when it was executed.

Having this longer stack trace may aide you with debugging which feature of Angular your code is interacting with asynchronously and help you narrow down where your problem is occuring.

```
debugging.html:16 Error: Finished
>>  at stepTwo (http://localhost:3030/examples/debugging.html:28:15)
    at ZoneDelegate.invokeTask (http://localhost:3030/node_modules/zone.js/dist/zone.js:265:35)
    at Zone.runTask (http://localhost:3030/node_modules/zone.js/dist/zone.js:154:47)
    at ZoneTask.invoke (http://localhost:3030/node_modules/zone.js/dist/zone.js:335:33)
    at data.args.(anonymous function) (http://localhost:3030/node_modules/zone.js/dist/zone.js:970:25)
  -------------   Elapsed: 101 ms; At: Wed Nov 16 2016 08:23:17 GMT-0500 (EST)   -------------  
    at Object.onScheduleTask (http://localhost:3030/node_modules/zone.js/dist/long-stack-trace-zone.js:83:18)
    at ZoneDelegate.scheduleTask (http://localhost:3030/node_modules/zone.js/dist/zone.js:242:49)
    at Zone.scheduleMacroTask (http://localhost:3030/node_modules/zone.js/dist/zone.js:171:39)
    at http://localhost:3030/node_modules/zone.js/dist/zone.js:991:33
    at setTimeout (eval at createNamedFn (http://localhost:3030/node_modules/zone.js/dist/zone.js:927:17), <anonymous>:3:37)
>>  at stepOne (http://localhost:3030/examples/debugging.html:23:9)
    at ZoneDelegate.invokeTask (http://localhost:3030/node_modules/zone.js/dist/zone.js:265:35)
    at Zone.runTask (http://localhost:3030/node_modules/zone.js/dist/zone.js:154:47)
  -------------   Elapsed: 105 ms; At: Wed Nov 16 2016 08:23:17 GMT-0500 (EST)   -------------  
    at Object.onScheduleTask (http://localhost:3030/node_modules/zone.js/dist/long-stack-trace-zone.js:83:18)
    at ZoneDelegate.scheduleTask (http://localhost:3030/node_modules/zone.js/dist/zone.js:242:49)
    at Zone.scheduleMacroTask (http://localhost:3030/node_modules/zone.js/dist/zone.js:171:39)
    at http://localhost:3030/node_modules/zone.js/dist/zone.js:991:33
    at setTimeout (eval at createNamedFn (http://localhost:3030/node_modules/zone.js/dist/zone.js:927:17), <anonymous>:3:37)
>>  at startAsync (http://localhost:3030/examples/debugging.html:33:9)
    at ZoneDelegate.invoke (http://localhost:3030/node_modules/zone.js/dist/zone.js:232:26)
    at Zone.run (http://localhost:3030/node_modules/zone.js/dist/zone.js:114:43)
```