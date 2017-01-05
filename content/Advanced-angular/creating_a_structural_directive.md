# 创建结构指令

我们将创建一个延迟组件或元素实例化的`appDelay`结构指令。 这可以潜在地用于美化效果或用用于手动处理组件加载的时间，无论是性能还是UX。

```typescript
@Directive({
  selector: '[appDelay]'
})
export class DelayDirective {
  constructor(
    private templateRef: TemplateRef<any>,
    private viewContainerRef: ViewContainerRef
  ) { }

  @Input()
  set appDelay(time: number): void { }
}
```

[View Example](https://plnkr.co/edit/80AGn8bR4CiyH0ceP8ws?p=preview)

我们使用相同的`@Directive`类装饰器作为属性指令，并以相同的方式定义一个选择器。 这里的一个很大的区别是，由于结构指令的性质绑定到模板，我们可以访问`TemplateRef，`一个表示指令附加的`template`标签的对象。 我们还以类似的方式添加一个输入属性，但这次使用一个`set`处理程序，所以我们可以在Angular 2执行绑定时执行一些代码。 我们以与Angular 2内置结构指令完全相同的方式绑定`delay` 。

```typescript
@Component({
  selector: 'app-root',
  template: `
    <div *ngFor="let item of [1,2,3,4,5,6]">
      <card *delay="500 * item">
        {{item}}
      </card>
    </div>
  `

})
export class AppComponent {
}
```

[View Example](https://plnkr.co/edit/80AGn8bR4CiyH0ceP8ws?p=preview)

注意，没有内容在渲染。 这是由于Angular 2模拟html模板标签，并且默认情况下不渲染任何子元素。 为了能够渲染这个内容，我们必须将`TemplateRef`给出的模板作为嵌入视图附加到视图容器。