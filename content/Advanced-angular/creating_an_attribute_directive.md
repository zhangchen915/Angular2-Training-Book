#创建属性指令
让我们从一个简单的按钮开始，将用户移动到不同的页面。
```ts
@Component({
  selector: 'visit-rangle',
  template: `
    <button type="button" (click)="visitRangle()">Visit Rangle</button>
  `
})
export class VisitRangleComponent {
  visitRangle() {
    location.href = 'https://rangle.io';
  }
}
```
查看示例
我们有礼貌，所以不是仅仅将用户发送到新页面，而是通过创建属性指令并将其附加到按钮，来询问他们是否确定。
```ts
@Directive({
  selector: `[confirm]`
})
export class ConfirmDirective {

  @HostListener('click', ['$event'])
  confirmFirst(event: Event) {
    return window.confirm('Are you sure you want to do this?');
  }

}
```
查看示例
指令通过使用所创建的@Directive一类的装饰，并指定一个选择器。对于伪指令，选择器名称必须为camelCase，并用方括号括起来，以指定它是属性绑定。我们使用的@HostListener装饰来偷听它附着的组件或元素上的事件。在这种情况下，我们会留意click事件，并传递这是由特殊给出的事件的详细信息$event关键字。接下来，我们要将此指令附加到我们之前创建的按钮。
```ts
template: `
  <button type="button" (click)="visitRangle()" confirm>Visit Rangle</button>
`
```
查看示例
然而，请注意，该按钮不能按预期工作。这是因为当我们收听点击事件并显示确认对话框时，组件的点击处理程序在指令的点击处理程序之前运行，两者之间没有通信。为此，我们需要重写我们的指令以使用组件的点击处理程序。
```ts
@Directive({
  selector: `[confirm]`
})
export class ConfirmDirective {
  @Input('confirm') onConfirmed: Function = () => {};

  @HostListener('click', ['$event'])
  confirmFirst() {
    const confirmed = window.confirm('Are you sure you want to do this?');

    if(confirmed) {
      this.onConfirmed();
    }
  }
}
```
查看示例
在这里，我们想要指定在发送确认对话框后需要执行什么操作，为此，我们创建一个类似于组件上的输入绑定。我们将使用我们的指令名称来进行这个绑定，我们的组件代码的变化如下：
  <button type="button" [confirm]="visitRangle">Visit Rangle</button>
查看示例
现在我们的按钮工作，正如我们的预期。我们可能希望能够自定义确认对话框的消息。为此，我们将使用另一个绑定。
@Directive({
  selector: `[confirm]`
})
export class ConfirmDirective {
  @Input('confirm') onConfirmed: Function = () => {};
  @Input() confirmMessage: string = 'Are you sure you want to do this?';

  @HostListener('click', ['$event'])
  confirmFirst() {
    const confirmed = window.confirm(this.confirmMessage);

    if(confirmed) {
      this.onConfirmed();
    }
  }
}
查看示例
我们的指令获取表示确认对话框中的消息，这是我们传递一个新的输入属性window.confirm调用。要利用这个新的输入属性，我们添加另一个绑定到我们的按钮。
    <button
      type="button"
      [confirm]="visitRangle"
      confirmMessage="Click ok to visit Rangle.io!">
      Visit Rangle
    </button>
查看示例
现在我们有一个按钮，可以自定义确认消息，在它移动到一个新的网址。