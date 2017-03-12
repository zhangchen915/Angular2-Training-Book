#NgIf 指令
ngIf指令根据表达式是真还是假有条件地呈现组件或元素

这里是我们的应用程序组件，我们将ngIf指令绑定到一个示例组件。
```typescript
@Component({
  selector: 'app',
  template: `
    <button type="button" (click)="toggleExists()">Toggle Component</button>
    <hr/>
    <if-example *ngIf="exists">
      Hello
    </if-example>
  `
})
export class AppComponent {
  exists: boolean = true;

  toggleExists() {
    this.exists = !this.exists;
  }
}
```
[查看示例](https://plnkr.co/edit/MTyYN0ntm1BNKE20HT7a?p=preview)
单击按钮将切换IfExampleComponent是否为DOM的一部分，而不仅仅是它是否可见。 这意味着每次点击按钮时，IfExampleComponent将被创建或销毁。 这可能是有昂贵的创建/销毁操作的组件的问题。 例如，一个组件可以有一个大的子子树或构造时进行多个HTTP调用。 在这些情况下，如果可能，最好避免使用ngIf。



⚡️ NgIf语法在 4.0.0-beta.0 后已经支持else。此外，条件值现在可以存储在局部变量中，供以后重用。当与`async`管道一起使用时特别有用。

```html
<div *ngIf="userObservable | async; else loading; let user">
  Hello {{user.last}}, {{user.first}}!
</div>
<template #loading>Waiting...</template>
```

注意当使用 then 时 *ngIf 所在的元素部分将被忽略。

```html
<element *ngIf="someExpression then thenRef else elseRef"></element>
<template #thenRef>AAA</template>
<template #elseRef>BBB</template>
```