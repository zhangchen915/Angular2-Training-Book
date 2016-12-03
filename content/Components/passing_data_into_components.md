#将数据传递到组件
该inputs属性定义了一组可以从组件父向下传递的参数。例如，我们可以修改Hello组件，以便name可以由父进行配置。
```ts
import {Component, Input} from '@angular/core';

@Component({
  selector: 'hello',
  template: '<p>Hello, {{name}}</p>'
})
export class Hello {
  @Input() name: string;
}
```
制作组件的要点不仅是封装，而且是可重用性。输入允许我们配置组件的特定实例。
我们现在可以使用我们的组件：
```html
<!-- 绑定到原始字符串 -->
<hello name="World"></hello>
<!-- 绑定到父作用域 -->
<hello [name]="name"></hello>
```
[查看示例](http://plnkr.co/edit/GbpCKy?p=preview)
与Angular 1.x不同，这是单向绑定。