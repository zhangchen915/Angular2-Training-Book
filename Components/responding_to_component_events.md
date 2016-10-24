#响应组件事件
Angular 2中的事件与它们在Angular 1.x中的工作类似。最大的变化是模板语法。
```ts
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
要通过输出从组件发送数据，请先定义outputs属性。它接受组件向其父组件公开的输出参数的列表。
```ts
import {Component, EventEmitter, Input, Output} from '@angular/core';

@Component({
  selector: 'counter',
  template: `
    <div>
      <p>Count: {{ count }}</p>
      <button (click)="increment()">Increment</button>
    </div>
  `
})
export class Counter {
  @Input() count: number = 0;
  @Output() result: EventEmitter = new EventEmitter();

  increment() {
    this.count++;
    this.result.emit(this.count);
  }
}
import {Component} from '@angular/core';

@Component({
  selector: 'app',
  template: `
    <div>
      Parent Num: {{ num }}<br />
      Parent Count: {{ parentCount }}
        <counter [count]="num" (result)="onChange($event)">
        </counter>
      </div>
  `
})
export class App {
  num: number;
  parentCount: number;

  constructor() {
    this.num = 0;
    this.parentcount = 0;
  }

  onChange(val: any) {
    this.parentCount = val;
  }
}
```
[查看示例](http://plnkr.co/edit/iwQePN?p=preview)
一组输入+输出绑定定义组件的公共API。在我们的模板中，我们使用`[squareBrackets]`传递输入，使用（括号）来处理输出。