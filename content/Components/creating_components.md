#创建组件

Angular 2中的组件基于这个想法。我们在类中定义组件的应用程序逻辑。为此，我们附上一个选择器和一个模板。

* 选择器：我们用它来告诉Angular创建和插入这个组件实例的元素属性。
* 模板：HTML的一种形式，它告诉Angular如何呈现这个组件。
```ts
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
要使用这个组件，我们只需添加`<hello></hello>`到我们的HTML，Angular将插入这些标签之间的视图的实例。
[查看示例](http://plnkr.co/edit/LmsR4psbJZwXH0c4lpMa?p=preview)