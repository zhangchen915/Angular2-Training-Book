#查看容器和嵌入视图
查看容器是可以附加一个或多个视图的容器。 视图表示要渲染的某种布局和渲染它的上下文。 视图容器锚定到组件，并负责生成其输出，因此这意味着更改附加到视图容器的视图会影响组件的最终渲染输出。
可以将两种类型的视图附加到视图容器：链接到组件的主机视图，以及链接到模板的嵌入视图。 由于结构指令与模板交互，我们有兴趣在这种情况下使用嵌入视图。
```ts
import {Directive, Input, TemplateRef, ViewContainerRef} from '@angular/core';

@Directive({
  selector: '[delay]'
})
export class DelayDirective {
  constructor(
    private templateRef: TemplateRef<any>,
    private viewContainerRef: ViewContainerRef
  ) { }

  @Input('delay')
  set delayTime(time: number): void {
    setTimeout(
      () => {
        this.viewContainerRef.createEmbeddedView(this.templateRef);
      },
      time);
  }
}
```
[查看示例](https://plnkr.co/edit/yjWcUE7zJgoUPc9NJR0n?p=preview)
指令通过注入ViewContainerRef访问视图容器。 嵌入视图通过调用ViewContainerRef的createEmbeddedView方法并传入模板来创建并附加到视图容器。 我们想使用我们的指令附加的模板，所以我们传入注入的TemplateRef。