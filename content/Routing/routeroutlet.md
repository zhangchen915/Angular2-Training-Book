# 动态添加路由组件

不同于单独定义每个路由的组件，使用`RouterOutlet`作为组件占位符; Angular 2会将被激活的路由的组件动态地添加到`<router-outlet> </ router-outlet>`元素中。

```typescript
import { Component } from '@angular/core';

@Component({
  selector: 'app',
  template: `
    <nav>
      <a [routerLink]="['/component-one']">Component One</a>
      <a [routerLink]="['/component-two']">Component Two</a>
    </nav>

    <router-outlet></router-outlet>
    <!-- Route components are added by router here -->
  `
})
export class AppComponent {}
```

在上面的例子中，当点击链接时，对应于指定路由的组件将放置在`<router-outlet> </ router-outlet>`元素之后。

[View Example](https://plnkr.co/edit/odsZq1pL43HtWcUr0taJ?p=preview)

> 用全屏模式运行示例，以查看URL的变化。