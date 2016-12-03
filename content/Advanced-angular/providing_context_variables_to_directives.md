#向指令提供上下文变量
假设我们要记录一些关于我们的指令如何影响组件的元数据，并使这些数据可用。 例如，在我们的delay指令中，我们进行了一个setTimeout调用，在JavaScript的单线程异步模型中意味着它可能不会在我们提供的确切时间之后运行。 我们将捕获它加载的确切时间，并使该变量在模板中可用。
```ts
export class DelayContext {
  constructor(private loadTime: number) { }
}

@Directive({
  selector: '[delay]'
})
export class DelayDirective {
  constructor(
    private templateRef: TemplateRef<DelayContext>,
    private viewContainerRef: ViewContainerRef
  ) { }

  @Input('delay')
  set delayTime(time: number): void {
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
我们对delay指令做了一些修改。 我们创建了一个新的DelayContext类，它包含我们想要提供给我们的指令的上下文。 在这种情况下，我们要捕获createEmbeddedView调用发生的实际时间，并使其在我们的指令中可用作loadTime。 我们还提供了我们的新类作为TemplateRef函数的泛型参数。 这使得静态分析，并让我们确保我们的调用createEmbeddedView传递类型DelayContext的变量。 在我们的createEmbeddedView调用中，我们传入我们的变量，它捕获了方法调用的时间

在使用delay的组件中，我们以访问ngFor中的变量的方式访问loadTime上下文变量。
```ts
@Component({
  selector: 'app',
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