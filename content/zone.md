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

通常，在异步事件链期间抛出的异常将仅包括在当前方法的堆栈跟踪中。

使用Zone.js跟踪所有的异步调用，它可以为我们提供更长，更详细的事件和调用的堆栈跟踪，让我们更容易找到导致异常的原因。

要在开发中启用长堆栈跟踪，您应该在代码中包括**long-stack-trace-zone**模块。 但最好不要包括在您的生产构建中，Angular将跳过设置更长的堆栈跟踪在生产模式（`@angular/core`中的`enableProdMode`）。

Angular will take care of forking and extending its own zone to display more meaningful stack traces.

Angular将负责分叉 forking 并扩展其自己的区域以显示更有意义的堆栈跟踪。

```
if (__PRODUCTION__) {
  enableProdMode();
} else {
  require('zone.js/dist/long-stack-trace-zone');
}
```

使用下面的代码，我们首先调用`startAsync`，触发一个`setTimeouts`链，导致一个未捕获的错误。

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

### 简单堆栈跟踪

这是一个典型的堆栈跟踪，您将在此场景中看到，没有Zone，只显示发生未处理异常的函数。

```
Uncaught Error: Finished(…)
  stepTwo @ debugging.html:28

```

### 详细的“长”堆栈跟踪

在下面的堆栈跟踪中，您可以看到在这个异步函数调用链中发生的事件的顺序，添加了“>>”以指出我们的函数。

你会注意到这个堆栈跟踪包括更多的信息，包括Zone自己的任务管理（例如`onScheduleTask`），以及从函数排队到执行时间的时间。

通过这个更长的堆栈跟踪可以帮助你调试Angular的特性，异步交互代码，并有助于更精确定位问题。

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