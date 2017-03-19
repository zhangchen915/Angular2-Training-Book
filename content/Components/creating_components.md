#创建组件

创建组件需要三步：
1. 从 @angular/core 引入 Component 装饰器
2. 创建一个类，并用 @Component 修饰
3. 在 @Component 中 ，设置selector、template 和 styles 等元数据

```typescript
import {Component} from '@angular/core';

@Component({
    selector: 'hello',
    template: '<p>Hello, {{name}}</p>'
})
export class Hello {
  name: string;

  constructor() {
    this.name = 'World';
  }
}
```
- *selector* （选择器）：我们用它来告诉Angular创建和插入这个组件实例的元素属性。
- *template* （模板）：HTML的一种形式，它告诉Angular如何呈现这个组件。下面的组件会将name变量的值插入到双括号`{{name}}`之间的模板中，在视图中呈现的是`<p>Hello World</p>`。

要使用这个组件，我们只需添加`<hello></hello>`到我们的HTML，Angular将插入这些标签之间的视图的实例。
[查看示例](http://plnkr.co/edit/LmsR4psbJZwXH0c4lpMa?p=preview)