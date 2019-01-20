# 使用辅助路由

Angular 2支持辅助路由的概念，它允许您在单个应用程序中设置和导航多个独立路由。 每个组件具有一个主路由和零个或多个辅助出口。 辅助出口必须在组件中具有唯一的名称。

为了定义辅助路由，我们必须首先添加一个命名的路由出口，其中要呈现辅助路由的内容。

这里有一个例子：

```typescript
import {Component} from '@angular/core';

@Component({
  selector: 'app',
  template: `
    <nav>
      <a [routerLink]="['/component-one']">Component One</a>
      <a [routerLink]="['/component-two']">Component Two</a>
      <a [routerLink]="[{ outlets: { 'sidebar': ['component-aux'] } }]">Component Aux</a>
    </nav>

    <div style="color: green; margin-top: 1rem;">Outlet:</div>
    <div style="border: 2px solid green; padding: 1rem;">
      <router-outlet></router-outlet>
    </div>

    <div style="color: green; margin-top: 1rem;">Sidebar Outlet:</div>
    <div style="border: 2px solid blue; padding: 1rem;">
      <router-outlet name="sidebar"></router-outlet>
    </div>
  `
})
export class AppComponent {

}
```

接下来，我们必须定义到应用程序的辅助路由的链接，以导航和呈现内容。

```html
<a [routerLink]="[{ outlets: { 'sidebar': ['component-aux'] } }]">
  Component Aux
</a>
```

[View Example](https://plnkr.co/edit/9fwfVblql0FsFb7MNnlX?p=preview)

每个辅助路由是独立的路由，可以拥有：

- 自己的子路由
- 自己的辅助路由
- 自己的路由参数
- 自己的浏览器历史记录栈