# 配置路由

## Base 标签

Base 标记必须在index.html的<head>标记中设置：

```
<base href="/">
```

> 在演示中，我们使用脚本标记来设置`base`标记。在实际应用中，必须如上设置。

## 路由定义对象

`Routes`类型是定义应用程序路由的路由数组。 这是我们可以设置预期的路径，我们想要使用的组件，我们希望我们的应用程序来理解它们。

每个路由可以有不同的属性; 一些常见的属性是：

- path - 应用程序在特定路由上时在浏览器中显示的URL
- component - 当应用程序在特定路由上时要呈现的组件
- redirectTo - 如果需要重定向路由; 每个路由可以具有在路由中定义的组件或重定向属性（在本章后面讨论）
- pathMatch - 默认为'prefix'的可选属性。 确定是匹配完整的网址还是仅匹配开头。 当定义一个具有空路径字符串的路径设置pathMatch为'full'时，否则它将匹配所有路径。
- children - 表示此路由的子路由的路由定义数组（本章后面将介绍）。

要使用路由，请创建 [路由配置数组](https://angular.io/docs/ts/latest/api/router/index/Route-interface.html)。

下面是`Routes`数组定义的示例：

```typescript
const routes: Routes = [
  { path: 'component-one', component: ComponentOne },
  { path: 'component-two', component: ComponentTwo }
];
```

[See Routes definition](https://angular.io/docs/ts/latest/api/router/index/Routes-type-alias.html)

## RouterModule

`RouterModule.forRoot`将`Routes`数组作为参数，并返回配置的路由器模块。 此路由器模块必须在应用程序模块的导入列表中指定。

```typescript
...
import { RouterModule, Routes } from '@angular/router';

const routes: Routes = [
  { path: 'component-one', component: ComponentOne },
  { path: 'component-two', component: ComponentTwo }
];

export const routing = RouterModule.forRoot(routes);

@NgModule({
  imports: [
    BrowserModule,
    routing
  ],
  declarations: [
    AppComponent,
    ComponentOne,
    ComponentTwo
  ],
  bootstrap: [ AppComponent ]
})
export class AppModule {
}

platformBrowserDynamic().bootstrapModule(AppModule);
```