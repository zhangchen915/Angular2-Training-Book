# 访问子组件类

## @ViewChild 和 @ViewChildren

@ViewChild和@ViewChildren装饰器提供对包含组件的子组件的访问。
@ViewChild是一个装饰器函数，它将组件类的名称作为其输入，并在要绑定的包含组件的模板中找到其选择器。 @ViewChild也可以传递一个模板引用变量。
例如，我们将组件类`Alert`绑定到其选择器`<my-alert>`，并将其分配给属性`alert。` 这使我们能够访问类方法，如`show()`。

```typescript
import {Component, ViewChild} from '@angular/core';
import {Alert} from './alert.component';

@Component({
  selector: 'app',
  template: `
    <my-alert>My alert</my-alert>
    <button (click)="showAlert()">Show Alert</button>`
})
export class App {
  @ViewChild(Alert) alert: Alert;

  showAlert() {
    this.alert.show();
  }
}
```

[View Example](http://plnkr.co/edit/5CpQzHbNIiS1k5YuYZdw?p=preview)

为了分离关注，我们通常希望子元素处理自己的行为并传递`@Input()`。 然而，它可能有助于保持结构通用。

当模板中有多个嵌入式组件时，我们还可以使用@ViewChildren。 它收集Alert 组件的实例列表，存储在与数组类似的QueryList对象中。

```typescript
import {Component, QueryList, ViewChildren} from '@angular/core';
import {Alert} from './alert.component';

@Component({
  selector: 'app',
  template: `
    <my-alert ok="Next" (close)="showAlert(2)">Step 1: Learn angular</my-alert>
    <my-alert ok="Next" (close)="showAlert(3)">Step 2: Love angular</my-alert>
    <my-alert ok="Close">Step 3: Build app</my-alert>
    <button (click)="showAlert(1)">Show steps</button>`
})
export class App {
  @ViewChildren(Alert) alerts: QueryList<Alert>;
  alertsArr = [];

  ngAfterViewInit() {
    this.alertsArr = this.alerts.toArray();
  }

  showAlert(step) {
    this.alertsArr[step - 1].show(); // step 1 is alert index 0
  }
}
```

[View Example](http://plnkr.co/edit/8Eak9DANedsZDHBHuLea?p=preview)

如上所示，给定类型为@ViewChild和@ViewChildren的子组件或子组件的列表分别使用它们的选择器从模板中选择。 另外@ViewChild和@ViewChildren都可以传递一个选择器字符串：

```typescript
import {Component, QueryList, ViewChild, ViewChildren} from '@angular/core';
import {Alert} from './alert.component';

@Component({
  selector: 'app',
  template: `
    <my-alert #first ok="Next" (close)="showAlert(2)">Step 1: Learn angular</my-alert>
    <my-alert ok="Next" (close)="showAlert(3)">Step 2: Love angular</my-alert>
    <my-alert ok="Close">Step 3: Build app</my-alert>
    <button (click)="showAlert(1)">Show steps</button>`
})
export class App {
  @ViewChild('first') alerts: Alert;
  @ViewChildren(Alert) alerts: QueryList<Alert>;
  alertsArr = [];

  ngAfterViewInit() {
    this.alertsArr = this.alerts.toArray();
  }

  showAlert(step) {
    this.first.show();
  }
}
```

注意，在调用'ngAfterViewInit'生命周期钩子之前，View子集不会被设置。

## @ContentChild 和 @ContentChildren

`@ContentChild`和`@ContentChildren`的工作方式与等效的`@ViewChild`和`@ViewChildren`相同，但是，主要区别是`@ContentChild`和`@ContentChildren`从组件中的[projected content（投影内容）](https://angular-2-training-book.rangle.io/handout/components/projection.html)中选择。

再次注意，在ngAfterContentInit组件生命周期钩子之前不会设置内容子项。

[View Example](http://plnkr.co/edit/IsivWgg8A6zKVSuOLfE8?p=preview)