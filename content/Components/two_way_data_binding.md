#使用双向数据绑定
双向数据绑定使用`ngModel`指令将输入和输出绑定组合为单个符号。

```html
<input [(ngModel)]="name" >
```
它幕后做的相当于：
```html
<input [ngModel]="name" (ngModelChange)="name=$event">
```
要创建一个支持双向绑定的组件，你必须定义一个`@Output`属性匹配`@Input`，但后缀为`Change`，例如：

`app/counter.component.ts`

```typescript
import { Component, Input, Output, EventEmitter } from '@angular/core';

@Component({
  selector: 'rio-counter',
  templateUrl: 'app/counter.component.html'
})
export class CounterComponent {
  @Input() count = 0;
  @Output() countChange = EventEmitter<number>();

  increment() {
    this.count++;
    this.countChange.emit(this.count);
  }
}
```

`app/counter.component.html`

```html
<div>
  <p>
    <ng-content></ng-content>
    Count: {{ count }} -
    <button (click)="increment()">Increment</button>
  </p>
</div>
```

[View Example](http://plnkr.co/edit/nkww1Ov2AWZRMHFyjhjl?p=preview)