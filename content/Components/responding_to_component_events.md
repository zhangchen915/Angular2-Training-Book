#响应组件事件
Angular 2中的事件与它们在Angular 1.x中的工作类似。最大的变化是模板语法。
```typescript
import {Component} from '@angular/core';

@Component({
  selector: 'counter',
  template: `
    <div>
      <p>Count: {{ num }}</p>
      <button (click)="increment()">Increment</button>
    </div>
  `
})
export class Counter {
  num: number = 0;

  increment() {
    this.num++;
  }
}
```
[查看示例](http://plnkr.co/edit/15wHrpea6GY7yLr7hl61?p=preview)

要通过 outputs 从组件发送数据，请先定义outputs属性。它接受组件向其父组件公开的输出参数的列表。

`app/counter.component.ts`

```typescript
import { Component, EventEmitter, Input, Output } from '@angular/core';

@Component({
  selector: 'rio-counter',
  templateUrl: 'app/counter.component.html'
})
export class CounterComponent {
  @Input()  count = 0;
  @Output() result = new EventEmitter<number>();

  increment() {
    this.count++;
    this.result.emit(this.count);
  }
}
```

`app/counter.component.html`

```html
<div>
  <p>Count: {{ count }}</p>
  <button (click)="increment()">Increment</button>
</div>
```

`app/app.component.ts`

```typescript
import { Component, OnChange } from '@angular/core';

@Component({
  selector: 'rio-app',
  templateUrl: 'app/app.component.html'
})
export class AppComponent implements OnChange {
  num = 0;
  parentCount = 0;

  ngOnChange(val: number) {
    this.parentCount = val;
  }
}
```

`app/app.component.html`

```html
<div>
  Parent Num: {{ num }}<br>
  Parent Count: {{ parentCount }}
  <rio-counter [count]="num" (result)="ngOnChange($event)">
  </rio-counter>
</div>
```

[查看示例](http://plnkr.co/edit/iwQePN?p=preview)

一组 input + output 绑定定义组件的公共API。在我们的模板中，我们使用 [方括号] 传递输入，使用（括号）来处理输出。