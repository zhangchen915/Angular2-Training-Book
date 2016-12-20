#状态管道
有两种类型的管道：

- 无状态管道是纯粹的功能，通过输入数据流动而不记住任何东西或引起可检测的副作用。 大多数管道是无状态的。 我们使用的CurrencyPipe和我们创建的长度管是无状态管的示例。
- 状态管道是能够管理它们转换的数据的状态的管道。 创建HTTP请求，存储响应并显示输出的管道是有状态的管道。 有状态管道应谨慎使用。

Angular 2提供 `AsyncPipe`，这是有状态的。

## AsyncPipe

AsyncPipe可以接收Promise或Observable作为输入，并自动订阅输入，最终返回发射的值。 它是有状态的，因为管道维护对输入的预订，并且其返回值取决于该预订。

```typescript
import {Component} from '@angular/core';
import {Observable} from 'rxjs/Observable';

@Component({
  selector: 'product-price',
  template: `
    <p>Total price of product is {{fetchPrice | async | currency: "CAD": true: "1.2-2"}}</p>
    <p>Seconds: {{seconds | async}}</p>
  `
})
export class ProductPrice {
  count: number = 0;
  fetchPrice: Promise<number> = new Promise((resolve, reject) => {
    setTimeout(() => resolve(10), 500);
  });

  seconds: Observable<number> = new Observable(observer => {
    setInterval(() => { observer.next(this.count++); }, 1000);
  });
}
```

[View Example](http://plnkr.co/edit/YrJEE5UBTlZ8HtfMGfR2?p=preview)

## 实现有状态管道

默认情况下，管道是无状态的。 我们必须通过将@Pipe装饰器的pure属性设置为false来声明一个管道是有状态的。 这个设置告诉Angular的变化检测系统每个周期检查这个管道的输出，无论它的输入是否改变。

```typescript
import {Pipe, PipeTransform, ChangeDetectorRef} from '@angular/core';
import {Observable} from 'rxjs/Observable';
import 'rxjs/add/observable/timer';
import 'rxjs/add/operator/take';

/**
 * On number change, animates from `oldNumber` to `newNumber`
 */
// naive implementation assumes small number increments
@Pipe({
  name: 'animateNumber',
  pure: false
})
export class AnimateNumberPipe implements PipeTransform {
  private currentNumber: number = null; // intermediary number
  private newNumber: number = null;
  private subscription;

  constructor(private cdRef: ChangeDetectorRef) {}

  transform(newNumber: number): string {
    if (this.newNumber === null) { // set inital value
      this.currentNumber = this.newNumber = newNumber;
    }
    if (newNumber !== this.newNumber) {
      if (this.subscription) {
        this.currentNumber = this.newNumber;
        this.subscription.unsubscribe();
      }
      this.newNumber = newNumber;
      const oldNumber = this.currentNumber;
      const direction = ((newNumber - oldNumber) > 0) ? 1 : -1;
      const numbersToCount = Math.abs(newNumber - oldNumber) + 1;
      this.subscription = Observable.timer(0, 100) // every 100 ms
        .take(numbersToCount)
        .subscribe(
          () => {
            this.currentNumber += direction;
            this.cdRef.markForCheck();
          },
          null,
          () => this.subscription = null
        );
    }

    return this.currentNumber;
  }
}
```

[View Example](http://plnkr.co/edit/PQwaKTYeUXWdS3mm2ojY?p=preview)