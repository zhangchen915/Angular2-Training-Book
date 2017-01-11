# 控制对路由的访问

要控制用户是否可以导航到或离开指定路由，请使用路由哨兵。

例如，我们可能希望一些路线只有在用户登录或接受条款和条件后才可访问。 我们可以使用路由哨兵来检查这些条件并控制对路由的访问。

路由哨兵还可以控制用户是否可以离开某个路由。 例如，假设用户已将信息键入页面上的表单，但尚未提交表单。 如果这时离开页面，他们将丢失信息。 如果用户尝试离开路由而不是提交或保存信息，我们可以提示用户。

## 在路由上注册路由哨兵

为了使用路由哨兵，我们必须在我们希望它们运行的特定路由上注册它们。

例如，假设我们有一个`accounts` 路由，只有登录的用户可以访问。 此页面还有表单，我们希望确保用户在离开帐户页面之前提交了未保存的更改。

在我们的路由配置中，我们可以添加我们的哨兵到该路由：

```typescript
import { Routes, RouterModule } from '@angular/router';
import { AccountPage } from './account-page';
import { LoginRouteGuard } from './login-route-guard';
import { SaveFormsGuard } from './save-forms-guard';

const routes: Routes = [
  { path: 'home', component: HomePage },
  {
    path: 'accounts',
    component: AccountPage,
    canActivate: [LoginRouteGuard],
    canDeactivate: [SaveFormsGuard]
  }
];

export const appRoutingProviders: any[] = [];

export const routing = RouterModule.forRoot(routes);
```

现在`LoginRouteGuard`将在`accounts` 路由激活时检查，`SaveFormsGuard`将在离开该路由时被检查。

## 实现CanActivate

让我们看一个例子激活哨兵检查用户是否登录：

```typescript
import { CanActivate } from '@angular/router';
import { Injectable } from '@angular/core';
import { LoginService } from './login-service';

@Injectable()
export class LoginRouteGuard implements CanActivate {

  constructor(private loginService: LoginService) {}

  canActivate() {
    return this.loginService.isLoggedIn();
  }
}
```

这个类通过实现`canActivate`函数实现了`CanActivate`功能。

当`canActivate`返回true时，用户可以激活路由。 当`canActivate`返回false时，用户无法访问路由。 在上面的例子中，我们允许用户登录时的访问。

`canActivate`还可以用于通知用户他们无法访问应用程序的该部分，或将它们重定向到登录页面。

[See Official Definition for CanActivate](https://angular.io/docs/ts/latest/api/router/index/CanActivate-interface.html)

## 实现 CanDeactivate

`CanDeactivate`的工作方式与`CanActivate`类似，但有一些重要的区别。 `canDeactivate`函数将被禁用的组件作为参数传递给函数：

```typescript
export interface CanDeactivate<T> {
  canDeactivate(component: T, route: ActivatedRouteSnapshot, state: RouterStateSnapshot):
      Observable<boolean>|Promise<boolean>|boolean;
}
```

我们可以使用该组件来确定用户是否可以停用。

```typescript
import { CanDeactivate } from '@angular/router';
import { Injectable } from '@angular/core';
import { AccountPage } from './account-page';

@Injectable()
export class SaveFormsGuard implements CanDeactivate<AccountPage> {

  canDeactivate(component: AccountPage) {
    return component.areFormsSaved();
  }

}
```

[See Official Definition for CanDeactivate](https://angular.io/docs/ts/latest/api/router/index/CanDeactivate-interface.html)

## 异步路由哨兵

`canActivate`和`canDeactivate`函数可以返回`boolean`类型的值，也可以返回`Observable <boolean>`（一个可解析为boolean的Observable）的值。 如果你需要做一个异步请求（如服务器请求）来确定用户是否可以导航到或离开路由，你可以简单地返回一个`Observable <boolean>`。 路由将等待，直到它被解决并且使用该值来决定是否可以访问。

例如，当用户导航离开时，您可以有一个对话服务请求用户确认导航。 对话服务返回一个`Observable <boolean>`，如果用户单击“确定”，该值将解析为true，如果用户单击“取消”，则该值将为false。

```typescript
  canDeactivate() {
    return dialogService.confirm('Discard unsaved changes?');
  }
```

[View Example](http://plnkr.co/edit/w1NCkGs0Tv5TjivYMdvt?p=preview)

[See Official Documentation for Route Guards](https://angular.io/docs/ts/latest/guide/router.html#!#guards)