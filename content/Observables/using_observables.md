#使用Observable
让我们来看看如何在Angular 2组件中创建和使用Observable的一个基本示例：
```ts
import {Component} from '@angular/core';
import {Observable} from 'rxjs/Observable';

@Component({
    selector: 'app',
    template: `
      <b>Angular 2 Component Using Observables!</b>

      <h6 style="margin-bottom: 0">VALUES:</h6>
      <div *ngFor="let value of values">- {{ value }}</div>

      <h6 style="margin-bottom: 0">ERRORs:</h6>
      <div>Errors: {{anyErrors}}</div>

      <h6 style="margin-bottom: 0">FINISHED:</h6>
      <div>Finished: {{ finished }}</div>

      <button style="margin-top: 2rem;" (click)="init()">Init</button>
    `
})
export class MyApp {

  private data: Observable<Array<number>>;
  private values: Array<number> = [];
  private anyErrors: boolean;
  private finished: boolean;

  constructor() {
  }

  init() {
      this.data = new Observable(observer => {
          setTimeout(() => {
              observer.next(42);
          }, 1000);

          setTimeout(() => {
              observer.next(43);
          }, 2000);

          setTimeout(() => {
              observer.complete();
          }, 3000);
      });

      let subscription = this.data.subscribe(
          value => this.values.push(value),
          error => this.anyErrors = true,
          () => this.finished = true
      );
  }

}
```
首先我们从rxjs / Observable中将Observable导入到我们的组件中。 接下来，在我们的构造函数中，我们创建一个新的Observable。 请注意，这将创建一个包含数字类型数据的Observable数据类型。 这说明Observable提供的数据流，以及使我们能够维护我们期望接收的数据类型的完整性。
接下来我们调用这个Observable的subscribe，它允许我们监听任何数据。 在订阅中，我们使用三个不同的回调：第一个在接收新值时被调用，第二个在出现的任何错误时调用，最后一个表示当传入数据的序列完成并成功时被调用的函数。
我们还可以使用forEach监听传入的数据。 forEach和subscribe之间的关键区别在于如何处理错误和完成回调。 forEach调用只接受“下一个值”回调作为参数; 它然后返回promise而不是订阅。
当Observable完成时，promise解析。 当Observable遇到错误时，promise被拒绝。

你可以认为Observable.of（1，2，3）.forEach（doSomething）在语义上等同于：
```ts
new Promise((resolve, reject) => {
  Observable.of(1, 2, 3).subscribe(
    doSomething,
    reject,
    resolve);
});
The forEach pattern is useful for a sequence of events you only expect to happen once.
export class MyApp {

  private data: Observable<Array<number>>;
  private values: Array<number> = [];
  private anyErrors: boolean;
  private finished: boolean;

  constructor() {
  }

  init() {
      this.data = new Observable(observer => {
          setTimeout(() => {
              observer.next(42);
          }, 1000);

          setTimeout(() => {
              observer.next(43);
          }, 2000);

          setTimeout(() => {
              observer.complete();
          }, 3000);

          this.status = "Started";
      });

      let subscription = this.data.forEach(v => this.values.push(v))
            .then(() => this.status = "Ended");
  }

}
```