# 延迟加载模块

使用模块将我们的应用的相关功能分组的另一个优点是能够按需加载那些片段。 延迟加载模块有助于减少启动时间。 通过延迟加载，我们的应用程序不需要立即加载所有内容，它只需要加载用户期望第一次加载时看到的内容。 延迟加载的模块只会在用户导航到其路由时加载。

为了显示这种关系，让我们开始定义一个简单的模块，作为我们的示例应用程序的根模块。

*app/app.module.ts*

```typescript
import { NgModule } from '@angular/core';
import { BrowserModule  } from '@angular/platform-browser';

import { AppComponent } from './app.component';
import { EagerComponent } from './eager.component';
import { routing } from './app.routing';

@NgModule({
  imports: [
    BrowserModule,
    routing
  ],
  declarations: [
    AppComponent,
    EagerComponent
  ],
  bootstrap: [AppComponent]
})
export class AppModule {}
```

到目前为止，这是一个非常常见的模块，依赖于`BrowserModule`，有一个路由机制和两个组件：`AppComponent`和`EagerComponent`。 现在，让我们专注于定义导航的应用程序（`AppComponent`）的根组件。

*app/app.component.ts*

```typescript
import { Component } from '@angular/core';

@Component({
  selector: 'app-root',
  template: `
    <h1>My App</h1>
    <nav>
      <a routerLink="eager">Eager</a>
      <a routerLink="lazy">Lazy</a>
    </nav>
    <router-outlet></router-outlet>
  `
})
export class AppComponent {}
```

我们的导航系统只有两条路： `eager` 和`lazy`。 要知道当点击它们时加载哪些路径，我们需要看看我们传递给根模块的路由对象。

*app/app.routing.ts*

```typescript
import { ModuleWithProviders } from '@angular/core';
import { Routes, RouterModule } from '@angular/router';

import { EagerComponent } from './eager.component';

const routes: Routes = [
  { path: '', redirectTo: 'eager', pathMatch: 'full' },
  { path: 'eager', component: EagerComponent },
  { path: 'lazy', loadChildren: 'lazy/lazy.module#LazyModule' }
];

export const routing: ModuleWithProviders = RouterModule.forRoot(routes);
```

这里我们可以看到，在我们的应用程序中的默认路径叫做`eager` ，它将加载`EagerComponent`。

*app/eager.component.ts*

```typescript
import { Component } from '@angular/core';

@Component({
  template: '<p>Eager Component</p>'
})
export class EagerComponent {}
```

但更重要的是，我们可以看到，每当我们试图去`lazy`的路径，我们会懒加载一个模块，方便地称为`LazyModule` 。 仔细观察那条路由的定义：

```typescript
{ path: 'lazy', loadChildren: 'lazy/lazy.module#LazyModule' }
```

这里有几个重要的事情要注意：

1. 我们使用属性`loadChildren`而不是`component` 。
2. 我们传递一个字符串而不是一个符号，以避免加载模块。
3. 我们不仅定义了模块的路径，还定义了类的名称。

除了它有自己的路由和一个名为`LazyComponent`的组件，`LazyModule`没有什么特别的。

*app/lazy/lazy.module.ts*

```typescript
import { NgModule } from '@angular/core';

import { LazyComponent }   from './lazy.component';
import { routing } from './lazy.routing';

@NgModule({
  imports: [routing],
  declarations: [LazyComponent]
})
export class LazyModule {}
```

> 如果我们定义类`LazyModule`作为文件的默认导出，我们不需要在`loadChildren`属性中定义类名称，如上所示。

 `routing` 对象非常简单，只定义导航到`lazy`路径时加载的默认组件。

*app/lazy/lazy.routing.ts*

```typescript
import { ModuleWithProviders } from '@angular/core';
import { Routes, RouterModule } from '@angular/router';

import { LazyComponent } from './lazy.component';

const routes: Routes = [
  { path: '', component: LazyComponent }
];

export const routing: ModuleWithProviders = RouterModule.forChild(routes);
```

注意，我们使用方法调用`forChild`而不是`forRoot`来创建路由对象。 我们应该在为特征模块创建路由对象时始终这样做，无论模块应该是`eagerly` 还是 `lazily`加载。

最后，我们的`LazyComponent`非常类似于`EagerComponent`，并且只是一些文本的占位符。

*app/lazy/lazy.component.ts*

```typescript
import { Component } from '@angular/core';

@Component({
  template: '<p>Lazy Component</p>'
})
export class LazyComponent {}
```

[View Example](https://plnkr.co/edit/SGsAFpGhFX3vg3PysjnX?p=preview)

当我们第一次加载应用程序时，`AppComponent`中的`AppModule`将在浏览器中加载，我们应该看到导航系统和文本“Eager Component”。 直到这一点，`LazyModule`没有被下载，只有当我们点击链接“Lazy”所需的代码将被下载，我们将在浏览器中看到消息“Lazy Component”。

我们有效地延迟加载了一个模块。