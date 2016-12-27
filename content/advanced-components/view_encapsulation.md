# 封装视图

视图封装定义组件中定义的模板和样式是否会影响整个应用程序，反之亦然。 Angular提供了三种封装策略：

- `Emulated` （默认） - 主HTML的样式传播到组件。 在此组件的@Component装饰器中定义的样式仅限于此组件。
- `Native`  - 来自主HTML的本地样式不会传播到组件。 在此组件的@Component装饰器中定义的样式仅限于此组件。


- `None` - 来自组件的样式传播回主HTML，因此对页面上的所有组件都可见。 请注意在应用程序中包含无和本地组件的应用程序。 所有具有无封装的组件将使用Native封装在所有组件中复制其样式。

```
@Component({
...
encapsulation: ViewEncapsulation.None,
styles: [ ... ]
})
export class Hello { ... }
```
[View Example](http://plnkr.co/edit/hE4Yl8MraQ1vswhQbQmx?p=preview)