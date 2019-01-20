# 传递可选参数

查询参数允许您将可选参数传递到路由，例如分页信息。

例如，在具有分页列表的路由上，网址可能如下所示，表示我们已加载第二个网页：

`localhost:3000/product-list?page=2`

> 查询参数和路由参数之间的关键区别在于[路由参数](https://angular-2-training-book.rangle.io/handout/routing/routeparams.html)对于确定路由是必要的，而查询参数是可选的。

## 传递查询参数

使用`[queryParams]`指令和`[routerLink]`来传递查询参数。例如：

```html
<a [routerLink]="['product-list']" [queryParams]="{ page: 99 }">Go to Page 99</a>
```

或者，我们可以使用`Router`服务通过JS跳转：

```typescript
  goToPage(pageNum) {
    this.router.navigate(['/product-list'], { queryParams: { page: pageNum } });
  }
```

## 读取查询参数

类似于读取 [路由参数](https://angular-2-training-book.rangle.io/handout/routing/routeparams.html)，`Router`服务返回一个Observable，我们可以订阅读取查询参数：

```typescript
import { Component } from '@angular/core';
import { ActivatedRoute, Router } from '@angular/router';

@Component({
  selector: 'product-list',
  template: `<!-- Show product list -->`
})
export default class ProductList {
  constructor(
    private route: ActivatedRoute,
    private router: Router) {}

  ngOnInit() {
    this.sub = this.route
      .queryParams
      .subscribe(params => {
        // Defaults to 0 if no query param provided.
        this.page = +params['page'] || 0;
      });
  }

  ngOnDestroy() {
    this.sub.unsubscribe();
  }

  nextPage() {
    this.router.navigate(['product-list'], { queryParams: { page: this.page + 1 } });
  }
}
```

[View Example](http://plnkr.co/edit/12oJNdtzGzqLoBQXmjq1?p=preview)

[See Official Documentation on Query Parameters](https://angular.io/docs/ts/latest/guide/router.html#!#query-parameters)