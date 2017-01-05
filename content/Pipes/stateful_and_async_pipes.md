#状态管道
有两种类型的管道：

- 无状态管道是纯粹的功能，通过输入数据流动而不记住任何东西或引起可检测的副作用。 大多数管道是无状态的。 我们使用的CurrencyPipe和我们创建的长度管是无状态管的示例。
- 状态管道是能够管理它们转换的数据的状态的管道。 创建HTTP请求，存储响应并显示输出的管道是有状态的管道。 有状态管道应谨慎使用。

Angular 2提供 `AsyncPipe`，这是有状态的。

## AsyncPipe

AsyncPipe可以接收`Promise`或`Observable`作为输入，并自动订阅输入，最终返回发射的值。 它是有状态的，因为管道维护对输入的预订，并且其返回值取决于该预订。

```typescript
@Component({
  selector: 'app-root',
  template: `
    <p>Total price of product is {{fetchPrice | async | currency:"CAD":true:"1.2-2"}}</p>
    <p>Seconds: {{seconds | async}} </p>
  `
})
export class AppComponent {
  fetchPrice = new Promise((resolve, reject) => {
    setTimeout(() => resolve(10), 500);
  });

  seconds = Observable.of(0).concat(Observable.interval(1000))
}
```

[View Example](http://plnkr.co/edit/YrJEE5UBTlZ8HtfMGfR2?p=preview)

## 实现有状态管道

默认情况下，管道是无状态的。 我们必须通过将`@Pipe`装饰器的`pure`属性设置为`false`来声明一个管道是有状态的。 这个设置告诉Angular的变化检测系统每个周期检查这个管道的输出，无论它的输入是否改变。

```typescript
// naive implementation assumes small number increments
@Pipe({
  name: 'animateNumber',
  pure: false
})
export class AnimateNumberPipe implements PipeTransform {
  private currentNumber: number = null; // intermediary number
  private targetNumber: number = null;

  transform(targetNumber: number): string {
    if (targetNumber !== this.targetNumber) {
      this.currentNumber = this.targetNumber || targetNumber;
      this.targetNumber = targetNumber;

      const difference = this.targetNumber - this.currentNumber

      Observable.interval(100)
        .take(difference)
        .subscribe(() => {
          this.currentNumber++;
        })
    }

    return this.currentNumber;
  }
}
```

[View Example](http://plnkr.co/edit/PQwaKTYeUXWdS3mm2ojY?p=preview)