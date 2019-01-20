# 定义子路由

当某些路由只能在其他路由中访问和查看时，可能适合将其创建为子路由。

例如：产品详细信息页面可能有一个标签式导航部分，默认显示产品概述。 当用户单击“技术规格”选项卡时，该部分将显示规格。

如果用户点击ID为3的产品，我们要显示产品详细信息页面，其中包含概述：

`localhost:3000/product-details/3/overview`

当用户点击 "Technical Specs":

`localhost:3000/product-details/3/specs`

`overview` 和 `specs` 作为 `product-details/:id`的子路由。 它们只能在产品详细信息中找到。

我们的子 `Routes` 看起来像这样：

```typescript
export const routes: Routes = [
  { path: '', redirectTo: 'product-list', pathMatch: 'full' },
  { path: 'product-list', component: ProductList },
  { path: 'product-details/:id', component: ProductDetails,
    children: [
      { path: '', redirectTo: 'overview', pathMatch: 'full' },
      { path: 'overview', component: Overview },
      { path: 'specs', component: Specs }
    ]
  }
];
```

这些子路由的组件将显示在哪里？ 就像我们对于根应用程序组件有一个`<router-outlet> </ router-outlet>`，我们在`ProductDetails`组件中有一个路由出口。 对应于`product-details`的子路由的组件将被放置在`ProductDetails`中的路由出口。

```typescript
import { Component, OnInit, OnDestroy } from '@angular/core';
import { ActivatedRoute } from '@angular/router';

@Component({
  selector: 'product-details',
  template: `
    <p>Product Details: {{id}}</p>
    <!-- Product information -->
    <nav>
      <a [routerLink]="['overview']">Overview</a>
      <a [routerLink]="['specs']">Technical Specs</a>
    </nav>
    <router-outlet></router-outlet>
    <!-- Overview & Specs components get added here by the router -->
  `
})
export default class ProductDetails implements OnInit, OnDestroy {
  id: number;

  constructor(private route: ActivatedRoute) {}

  ngOnInit() {
    this.sub = this.route.params.subscribe(params => {
       this.id = +params['id']; // (+) converts string 'id' to a number
    });
  }

  ngOnDestroy() {
    this.sub.unsubscribe();
  }
}
```

或者，我们可以将`overview`路由URL简单地指定为：

`localhost:3000/product-details/`

```typescript
export const routes: Routes = [
  { path: '', redirectTo: 'product-list', pathMatch: 'full' },
  { path: 'product-list', component: ProductList },
  { path: 'product-details/:id', component: ProductDetails,
    children: [
      { path: '', component: Overview },
      { path: 'specs', component: Specs }
    ]
  }
];
```

由于`product-details` 的`Overview`子路由具有空路径，因此默认情况下将加载它。`specs`子路由保持不变。

[View Example with child routes](https://plnkr.co/edit/UpHEteaI4Bypaf0AkD4B?p=preview)

[View Example with route params & child routes](https://plnkr.co/edit/7SGuVQpkcTvj1CFct8rx?p=preview)

> 查看以全屏模式运行的示例，以查看URL变化。

## 访问父路由的参数

在上述示例中，假设`product-details` 的子路由需要产品的ID以获取规范或概述信息。子路由组件可以访问父路由的参数，如下所示：

```typescript
export default class Overview {
  parentRouteId: number;
  private sub: any;

  constructor(private router: Router,
    private route: ActivatedRoute) {}

  ngOnInit() {
    // Get parent ActivatedRoute of this route.
    this.sub = this.router.routerState.parent(this.route)
      .params.subscribe(params => {
        this.parentRouteId = +params["id"];
      });
  }

  ngOnDestroy() {
    this.sub.unsubscribe();
  }
}
```

[View Example child routes accessing parent's route parameters](https://plnkr.co/edit/b0Knfv929tfRdsqpXHgP?p=preview)

> 查看以全屏模式运行的示例，以查看URL变化。

## 链接

路由可以使用`/`，或`../`;这告诉Angular 2在路由树链接到哪里。

| Prefix | Looks in                          |
| ------ | --------------------------------- |
| `/`    | Root of the application           |
| none   | Current component children routes |
| `../`  | Current component parent routes   |

例如：

```html
<a [routerLink]="['route-one']">Route One</a>
<a [routerLink]="['../route-two']">Route Two</a>
<a [routerLink]="['/route-three']">Route Three</a>
```

在上面的例子中，路由一链接到当前路由的子节点。 路由二链接到当前路由的兄弟。 路由三链接到根组件的子节点（如果当前路由是根组件，则与路由一等效）。

[View Example with linking throughout route tree](https://plnkr.co/edit/HX9LFuysyKmxAngv1nN3?p=preview)

> 查看以全屏模式运行的示例，以查看URL变化。