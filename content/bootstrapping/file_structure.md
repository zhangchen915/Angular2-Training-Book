#了解文件结构

让我们开始使用单个组件创建一个空的Angular 应用程序。为此，我们需要以下文件：

- *app/app.component.ts* - 这是我们定义根组件的地方
- *app/app.module.ts* - 要引导的Angular模块的入口
- *index.html* - 这是组件将被呈现的页面
- *app/main.ts* - 是将组件和页面组合在一起的胶水

*app/app.component.ts*

```typescript
import { Component } from '@angular/core'

@Component({
    selector: 'app-root',
    template: '<b>Bootstrapping an Angular Application</b>'
})
export class AppComponent { }
```

*index.html*

```html
<body>
    <app-root>Loading...</app-root>
</body>
```

*app/app.module.ts*

```typescript
import { BrowserModule }  from '@angular/platform-browser';
import { NgModule } '@angular/core';
import { AppComponent } from './app.component'

@NgModule({
  imports: [BrowserModule],
  declarations: [AppComponent],
  bootstrap: [AppComponent]
})
export class AppModule {
}
```

*app/main.ts*

```typescript
import { platformBrowserDynamic } from '@angular/platform-browser-dynamic';
import { AppModule } from './app.module';

platformBrowserDynamic().bootstrapModule(AppModule);
```

如果你使用Ahead-of-Time（AoT）编译，你将编写`main.ts`如下。

```typescript
import { platformBrowser} from '@angular/platform-browser';
import { AppModuleNgFactory } from '../aot/app/app.module.ngfactory';

platformBrowser().bootstrapModuleFactory(AppModuleNgFactory);
```

[View Example](https://plnkr.co/edit/X0EBXA?p=preview)

引导程序进程 *main.ts*，这是应用程序的主入口点。 `AppModule`作为我们应用程序的根模块运行。模块被配置为使用`AppComponent`作为引导的组件，并将在任何遇到的`app-root`HTML元素上呈现。

*index.html* 文件中有一个`app`HTML元素，我们使用*app/main.ts*来导入`AppModule`组件和`platformBrowserDynamic().bootstrapModule`函数，并启动该进程。如上所示，您可以选择使用**AoT**，在这种情况下，您将使用工厂，在示例中是`AppModuleNgFactory`和`bootstrapModuleFactory` 。

为什么Angular 以这种方式 bootstrap 自身？ 那么实际上有一个很好的理由。 由于Angular不是基于Web的框架，我们可以编写将在NativeScript或Cordova或任何其他可以托管Angular应用程序的环境中运行的组件。

然后，神奇的是在我们的引导过程 - 我们可以导入我们想要使用的平台，这取决于我们所处的运行环境。在我们的示例中，由于我们在浏览器中运行Angular 2应用程序，因此我们使用`@angular/platform-browser-dynamic`中的bootstrapping进程。

这也是一个好主意，将 bootstrapping 程序放在自己单独的*main.ts*文件中。这使得测试更容易（因为组件与`bootstrap` 调用隔离），更容易重用，并为我们的应用程序提供更好的组织和结构。

还有更多的关于理解Angular模块和`@NgModule`的内容将在稍后介绍，但现在这已经足够开始。