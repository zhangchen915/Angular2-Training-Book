#将数据传递到组件
有两种方式将数据传递到组件：“属性绑定”和“事件绑定”。 在Angular 2中，数据和事件变化检测从上到下发生从父级到子级。 然而对于Angular 2事件，我们可以使用DOM事件传递模型，其中事件从下到上从子到父。 因此，当涉及可撤消事件传播时，Angular 2事件可以像普通HTML DOM事件一样对待。

`@Input()`装饰器定义了一组可以从父组件传递的参数。例如，我们可以修改`HelloComponent`组件，以便`name`可以由父提供。

```typescript
import {Component, Input} from '@angular/core';

@Component({
  selector: 'hello',
  template: '<p>Hello, {{name}}</p>'
})
export class Hello {
  @Input() name: string;
}
```
制作组件的要点不仅是封装，而且是可重用性。`@Input()`允许我们配置组件的特定实例。

我们现在可以使用我们的组件：

```html
<!-- 绑定到原始字符串 -->
<hello name="World"></hello>
<!-- 绑定到父作用域 -->
<hello [name]="name"></hello>
```
[查看示例](http://plnkr.co/edit/GbpCKy?p=preview)

> 与Angular 1.x不同，这是单向绑定。