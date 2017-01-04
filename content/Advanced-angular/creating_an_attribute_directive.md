#创建属性指令
让我们从一个简单的按钮开始，让用户跳转到不同的页面。
```typescript
@Component({
  selector: 'app-visit-rangle',
  template: `
    <button
      type="button"
      (click)="visitRangle()">
      Visit Rangle
    </button>
  `
})
export class VisitRangleComponent {
  visitRangle() {
    location.href = 'https://rangle.io';
  }
}
```
[View Example](https://plnkr.co/edit/9ANDvP9C1p2jSZW2s4LX?p=preview)

我们有礼貌，所以不是仅仅将用户发送到新页面，而是通过创建属性指令并将其附加到按钮，来询问他们是否确定跳转。

```typescript
@Directive({
  selector: `[appConfirm]`
})
export class ConfirmDirective {
  @HostListener('click', ['$event'])
  confirmFirst(event: Event) {
    return window.confirm('Are you sure you want to do this?');
  }

}
```
[View Example](https://plnkr.co/edit/KMfnzrmSx0ywKp6ztaNN?p=preview)

通过在类上使用`@Directive`装饰器并指定选择器来创建指令。对于指令，选择器名称必须为camelCase，并用方括号括起来，以指定它是属性绑定。我们使用`@HostListener`装饰器来监听它所附加的组件或元素上的事件。 在这种情况下，我们正在观察点击事件并传递`$event`关键字给出的事件详细信息。 接下来，我们要将此指令附加到我们之前创建的按钮。

```ts
template: `
  <button
    type="button"
    (click)="visitRangle()"
    appConfirm>
    Visit Rangle
  </button>
`
```
[View Example](https://plnkr.co/edit/KMfnzrmSx0ywKp6ztaNN?p=preview)

然而，请注意，该按钮不能按预期工作。这是因为当我们监听点击事件并显示确认对话框时，组件的点击处理程序在指令的点击处理程序之前运行，两者之间没有通信。为此，我们需要重写我们的指令以使用组件的点击处理程序。

```typescript
@Directive({
  selector: `[appConfirm]`
})
export class ConfirmDirective {
  @Input() appConfirm = () => {};

  @HostListener('click', ['$event'])
  confirmFirst() {
    const confirmed = window.confirm('Are you sure you want to do this?');

    if(confirmed) {
      this.appConfirm();
    }
  }
}
```
[View Example](https://plnkr.co/edit/OBuN06R0hmcnpGu01Z7I?p=preview)

在这里，我们想要指定在发送确认对话框后需要执行什么操作，为此，我们创建一个类似于组件上的输入绑定。 我们将使用我们的指令名称来进行这个绑定，我们的组件代码的变化如下：

```
  <button
    type="button"
    [appConfirm]="visitRangle">
    Visit Rangle
  </button>

```

[View Example](https://plnkr.co/edit/OBuN06R0hmcnpGu01Z7I?p=preview)

现在我们的按钮按预期工作了。 我们可能希望能够自定义确认对话框的消息。 为此，我们将使用另一个绑定。

```typescript
@Directive({
  selector: `[appConfirm]`
})
export class ConfirmDirective {
  @Input() appConfirm = () => {};
  @Input() confirmMessage = 'Are you sure you want to do this?';

  @HostListener('click', ['$event'])
  confirmFirst() {
    const confirmed = window.confirm(this.confirmMessage);

    if(confirmed) {
      this.appConfirm();
    }
  }
}
```

[View Example](https://plnkr.co/edit/S8pkKyrdF4jB7HlVQ76n?p=preview)

我们的指令获得一个新的输入属性，它表示确认对话框消息，我们传递给`window.confirm`调用。要利用这个新的输入属性，我们添加另一个绑定到我们的按钮。

```
<button
  type="button"
  [appConfirm]="visitRangle"
  confirmMessage="Click ok to visit Rangle.io!">
  Visit Rangle
</button>

```

[View Example](https://plnkr.co/edit/S8pkKyrdF4jB7HlVQ76n?p=preview)

现在我们有一个可以在跳转到一个新页面之前自定义确认消息的按钮。