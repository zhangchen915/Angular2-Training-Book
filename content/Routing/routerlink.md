# 定义路由之间的链接

## RouterLink

使用RouterLink指令添加链路到路由。 例如，以下代码定义了在路径`component-one`

的路由的链接。

```
<a [routerLink]="['/component-one']">Component One</a>
```

## Navigating Programmatically

或者，您可以通过调用`route` 上的`navigate` 功能导航到路由：

```
this.router.navigate(['/component-one']);
```