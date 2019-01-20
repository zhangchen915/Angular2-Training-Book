# Redux和组件架构

在上面的例子中，我们的`counter` 组件是一个智能组件。 它知道Redux，state的结构和它需要调用的动作。 理论上，你可以把这个组件放到你的应用程序的任何区域，只是让它工作。 但它将紧紧绑定到那个特定的状态切片和那些具体的动作。 例如，如果我们想要多个计数器跟踪页面上的不同内容怎么办？ 例如，计算红色点击次数与蓝色点击次数。

为了帮助组件更通用和可重用，值得尝试将它们分成*容器*组件和*演示*组件。

|                | 容器组件                      | 演示组件                                     |
| -------------- | ------------------------- | ---------------------------------------- |
| Location       | Top level, route handlers | Middle and leaf components               |
| Aware of Redux | Yes                       | No                                       |
| To read data   | Subscribe to Redux state  | Read state from @Input properties        |
| To change data | Dispatch Redux actions    | Invoke callbacks from @Output properties |

[redux docs](http://redux.js.org/docs/basics/UsageWithReact.html).

记住这一点，让我们重构我们的 `counter` 是一个演示组件。 首先，让我们修改我们的`app-container`，在它上面有两个计数器组件，就像我们现在有的一样。

```typescript
import { Component } from '@angular/core';

@Component({
  selector: 'simple-redux'
  template: `
    <div>
      <h1>Redux: Two components, one state.</h1>
      <div style="float: left; border: 1px solid red;">
        <h2>Click Counter</h2>
        <counter></counter>
      </div>
      <div style="float: left; border: 1px solid blue;">
        <h2>Curse Counter</h2>
        <counter></counter>
      </div>
    </div>
  `
})
export class SimpleRedux {}
```

[View Example](https://plnkr.co/edit/w9qg7UklSryt4ujmCpTy?p=preview)

如您在示例中可以看到的，当单击按钮时，两个组件中的数字将同步更新。 这是因为计数器组件耦合到特定的状态和动作。

看看这个例子，你可以看到已经有一个 *app/reducers/curse-reducer.ts* 和 *app/actions-curse-actions.ts*。 它们几乎与计数器动作和计数器reducer相同，我们只是想创建一个新的reducer来保持它的状态。

要将计数器组件从智能组件转换为哑组件，我们需要更改它以将数据和回调传递到其中。 为此，我们将使用`@Inputproperties`将数据传递到组件中，并将操作回调作为`@Output`属性。

我们现在有一个很好的可重用的演示组件，它不知道Redux或我们的应用程序状态。

*app/components/counter-component.ts*

```typescript
import { Component, Input, Output, EventEmitter } from '@angular/core';
import { Observable } from 'rxjs';

@Component({
  selector: 'counter',
  template: `
  <p>
    Clicked: {{ counter | async }} times
    <button (click)="increment.emit()">+</button>
    <button (click)="decrement.emit()">-</button>
    <button (click)="incrementIfOdd.emit()">Increment if odd</button>
    <button (click)="incrementAsync.emit()">Increment async</button>
  </p>
  `
})
export class Counter {
  @Input() counter: Observable<number>;
  @Output() increment = new EventEmitter<void>();
  @Output() decrement = new EventEmitter<void>();
  @Output() incrementIfOdd = new EventEmitter<void>();
  @Output() incrementAsync = new EventEmitter<void>();
}
```

接下来，让我们修改主应用的容器，将这些输入和输出连接到模板。

`@Component` *app/src/containers/app-containter.ts*

```typescript
@Component({
  selector: 'simple-redux',
  providers: [ CounterActions, CurseActions ],
  template: `
  <div>
    <h1>Redux: Presentational Counters</h1>
    <div style="float: left; border: 1px solid red;">
      <h2>Click Counter</h2>
      <counter [counter]="counter$"
          (increment)="counterActions.increment()"
          (decrement)="counterActions.decrement()"
          (incrementIfOdd)="counterActions.incrementIfOdd()"
          (incrementAsync)="counterActions.incrementAsync()">
      </counter>
    </div>
    <div style="float: left; border: 1px solid blue;">
      <h2>Curse Counter</h2>
      <counter [counter]="curse$"
          (increment)="curseActions.castCurse()"
          (decrement)="curseActions.removeCurse()"
          (incrementIfOdd)="curseActions.castIfOdd()"
          (incrementAsync)="curseActions.castAsync()">
      </counter>
    </div>
  </div>
    `
})
```

此时，模板正在尝试调用我们的两个ActionCreatorServices，`CounterActions`和`CurseActions`上的操作; 我们只需要使用依赖注入来住它们：

*app/src/containers/app-container.ts*

```typescript
import { Component, View, Inject, OnDestroy, OnInit } from '@angular/core';
import { select } from 'ng2-redux';
import { Observable } from 'rxjs';
import { CounterActions } from '../actions/counter-actions';
import { CurseActions } from '../actions/curse-actions';

@Component({ /* see above .... */})
export class SimpleRedux {
  @select() counter$: Observable<number>;
  @select() curse$: Observable<number>;

  constructor(
    public counterActions: CounterActions,
    public curseActions: CurseActions) {
    }
}
```

[View Ng2-Redux Example](https://plnkr.co/edit/Ci7RDJPIcu43AD3zSZ1O?p=preview) [View Ngrx Example](https://plnkr.co/edit/2FraZ1rGDVoamX1Qtwv6?p=preview)

我们的两个`Observable`， `counter$` 和 `curse$` ，现在将在每次相关store属性由系统的其余部分更新时使用新值更新。