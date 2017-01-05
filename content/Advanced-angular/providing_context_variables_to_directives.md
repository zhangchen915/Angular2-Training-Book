#向指令提供上下文变量
假设我们要记录一些关于我们的指令如何影响组件的元数据，并使这些数据可用。 例如，在我们的`appDelay` 指令中，我们进行了一个`setTimeout`调用，在JavaScript的单线程异步模型中意味着它可能不会在我们提供的确切时间之后运行。 我们将捕获它确切的加载时间，并使该变量在模板中可用。

```typescript
export class DelayContext {
  constructor(private loadTime: number) { }
}

@Directive({
  selector: '[appDelay]'
})
export class DelayDirective {
  constructor(
    private templateRef: TemplateRef<DelayContext>,
    private viewContainerRef: ViewContainerRef
  ) { }

  @Input()
  set appDelay(time: number): void {
    setTimeout(
      () => {
        this.viewContainerRef.createEmbeddedView(
          this.templateRef,
          new DelayContext(performance.now())
        );
      },
      time);
  }
}
```
[View Example](https://plnkr.co/edit/GmjxiDSbv78zbBFqw8yv?p=preview)

我们对`appDelay` 指令做了一些修改。 我们创建了一个新的`DelayContext`类，它包含我们想要提供给我们的指令的上下文。 在这种情况下，我们要捕获`createEmbeddedView`调用发生的实际时间，并使其在我们的指令中可用作`loadTime`。 我们还提供了我们的新类作为`TemplateRef`函数的泛型参数。 这使得静态分析，并让我们确保我们的调用`createEmbeddedView`传递类型`DelayContext`的变量。 在我们的`createEmbeddedView`调用中，我们传入我们的变量，它捕获了方法调用的时间。

在使用`appDelay` 的组件中，我们以访问`ngFor`中的变量的方式访问`loadTime`上下文变量。

```typescript
@Component({
  selector: 'app-root',
  template: `
    <div *ngFor="let item of [1,2,3,4,5,6]">
      <card *delay="500 * item; let loaded = loadTime">
        <div class="main">{{item}}</div>
        <div class="sub">{{loaded | number:'1.4-4'}}</div>
      </card>
    </div>
  `
})
```
[查看示例](https://plnkr.co/edit/pSv4JsGhxxwzJOh9qSNj?p=preview)