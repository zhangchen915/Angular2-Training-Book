# 使用路由参数

假设我们正在创建一个显示产品列表的应用程序。当用户点击列表中的产品时，我们要显示一个页面，显示该产品的详细信息。为此你必须：

- 添加路由参数ID
- 将路由链接到参数
- 添加读取参数的服务。

## 声明路由参数

显示特定产品详细信息的组件的路由需要该产品ID的路由参数。我们可以使用以下`Router`实现：

```typescript
export const routes: Routes = [
  { path: '', redirectTo: 'product-list', pathMatch: 'full' },
  { path: 'product-list', component: ProductList },
  { path: 'product-details/:id', component: ProductDetails }
];
```

注意：`product-details`路由的路径中的`:id` ，它将参数放在路径中。例如，要查看ID为5的产品的产品详细信息页面，必须使用以下URL：`localhost:3000/product-details/5`

## 链接到带参数的路由

在`ProductList`组件中，您可以通过ID显示产品列表。每个产品都有一个链接到`product-details`的路由：

```html
<a *ngFor="let product of products"
  [routerLink]="['/product-details', product.id]">
  {{ product.name }}
</a>
```

注意，`routerLink`指令传递一个数组，该数组指定路径和路由参数。或者，我们可以使用JS跳转：

```typescript
goToProductDetails(id) {
  this.router.navigate(['/product-details', id]);
}
```

## 读取路由参数

`ProductDetails`组件必须读取参数，然后根据参数中给出的ID加载产品。 `ActivatedRoute`服务提供了一个`params` Observable，我们可以订阅它来获取路由参数（见 [Observables](https://angular-2-training-book.rangle.io/handout/observables/)）。​

```typescript
import { Component, OnInit, OnDestroy } from '@angular/core';
import { ActivatedRoute } from '@angular/router';

@Component({
  selector: 'product-details',
  template: `
    <div>
      Showing product details for product: {{id}}
    </div>
  `,
})
export class LoanDetailsPage implements OnInit, OnDestroy {
  id: number;
  private sub: any;

  constructor(private route: ActivatedRoute) {}

  ngOnInit() {
    this.sub = this.route.params.subscribe(params => {
       this.id = +params['id']; // (+) converts string 'id' to a number

       // In a real app: dispatch action to load the details here.
    });
  }

  ngOnDestroy() {
    this.sub.unsubscribe();
  }
}
```

> `ActivatedRoute`上的`params`属性是一个`Observable`的原因是路由器在导航到同一个组件时可能无法重新创建组件。在这种情况下，参数可能会改变，而不会重新创建组件。

[View Basic Example](https://plnkr.co/edit/YNMw1oaAeLSxIjCZZf2W?p=preview)

[View Example with Programmatic Route Navigation](https://plnkr.co/edit/Ea6lSIx8XD4yj9JuX4V7?p=preview)

> 查看以全屏模式运行的示例，以查看URL变化。