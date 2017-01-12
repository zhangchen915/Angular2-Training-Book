# 使用 Redux 与 Components

我们将使用来自Ng2-Redux的 [选择模式](https://github.com/angular-redux/ng2-redux#the-select-pattern)(https://github.com/angular-redux/ng2-redux#the-select-pattern)将我们的组件绑定到store。为了演示它是如何工作的，让我们来看看一个带有计数器组件的小例子。

## 计数器示例

让我们从建立一个计数器组件开始。这个组件将负责跟踪其被点击多少次并显示该数量。

*app/components/counter-component.ts*

```typescript
import { Component } from '@angular/core';
import { select } from 'ng2-redux';
import { Observable } from 'rxjs';
import { CounterActions } from '../actions/counter-actions';

@Component({
  selector: 'counter',
  providers: [ CounterActions ],
  template: `
  <p>
    Clicked: {{ counter$ | async }} times
    <button (click)="actions.increment()">+</button>
    <button (click)="actions.decrement()">-</button>
    <button (click)="actions.incrementIfOdd()">Increment if odd</button>
    <button (click)="actions.incrementAsync()">Increment async</button>
  </p>
  `
})
export class Counter {
  @select() counter$: Observable<number>;

  constructor(public actions: CounterActions) {}
}
```

[View Example](https://plnkr.co/edit/pujePgvmkyKHurXtOS3k?p=preview)

现在应该熟悉模板语法，使用异步管道显示一个`Observable`计数器，并处理一些点击事件。

在这种情况下，点击事件绑定到从`CounterActions` ActionCreatorService调用我们的动作创建者的表达式。

让我们来看看`@select`的使用。

`@select`是Ng2-Redux的一个功能，它旨在帮助您以声明方式将 store 的状态附加到组件。您可以将其附加到组件类的属性，Ng2-Redux将创建一个`Observable`并将其绑定到该属性。

在这种情况下，`@select`没有参数，因此Ng2-Redux将查找与类变量名称相同的store属性。它省略了尾随的`$`，因为这只是`Observables`的命名约定。

所以现在，任何时间`store.counter`由reducer更新，`counter$`将收到新的值并且`| async`将在模板中更新它。

请注意，`@select`支持各种各样的参数，以允许您以非常大的灵活性选择部分Redux存储。有关更多详细信息，请参阅 [Ng2-Redux](https://github.com/angular-redux/ng2-redux#the-select-pattern) 文档。

Ng2-Redux“选择模式”样式与`react-redux`使用的“连接”样式有点不同;然而通过使用Angular 2的DI和TypeScript的装饰器，我们可以有一个很好的声明式绑定，其中大部分的工作是在模板中完成。我们还获得`Observa～bles`和`OnPush`变化检测的功能，以获得更好的性能。

无论哪种方式，我们仍然从Redux的基本原理和单向数据流中获益。