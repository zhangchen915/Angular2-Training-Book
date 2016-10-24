#了解文件结构

要开始，让我们使用单个组件创建一个裸机Angular 2应用程序。为此，我们需要以下文件：
应用程序/ app.component.ts -这是我们确定我们的根组件
应用程序/ app.module.ts -条目角模块进行自举
index.html的 -这是组件将在呈现的页面
应用程序/ index.ts -是结合了组件和页面连接在一起的粘合剂
app / app.component.ts
```
import {Component} from '@angular/core'

@Component({
    selector: 'app',
    template: '<b>Bootstrapping an Angular 2 Application</b>'
})

export class MyApp {}
index.html
...
<body>
    <app>Loading...</app>
</body>
...
app / app.module.ts
import { BrowserModule }  from '@angular/platform-browser';
import { NgModule } '@angular/core';
import { MyApp } from './app.component'

@NgModule({
  imports: [BrowserModule],
  declarations: [MyApp],
  bootstrap: [MyApp]
})
export class MyAppModule {

}
app / index.ts
import { platformBrowserDynamic } from '@angular/platform-browser-dynamic'
import { MyAppModule } from './app.module'

platformBrowserDynamic().bootstrapModule(MyAppModule)
```
查看示例
这是应用程序的主要入口点。在MyAppModule 为我们的应用程序的根模块进行操作。该模块被配置为使用MyApp作为成分来引导，并且将任何上呈现app遇到HTML元素。1

有一个app在HTML元素的index.html文件，我们使用应用程序/ index.ts导入MyAppModule部件和platformBrowserDynamic().bootstrapModule功能和kickstart的过程。
为什么Angular 2以这种方式自举呢？实际上有一个很好的理由。由于Angular 2不是基于Web的框架，我们可以编写将在NativeScript或Cordova或任何其他可以托管Angular 2应用程序的环境中运行的组件。
然后，神奇的是在我们的引导过程 - 我们可以导入我们想要使用的平台，这取决于我们正在经营的环境。在我们的例子，因为我们运行我们在浏览器角2应用程序，我们使用中发现的引导过程@angular/platform-browser-dynamic。
这也是一个好主意，离开引导过程中它自己单独index.ts文件。这使得更容易进行测试（因为组分从分离bootstrap呼叫），更容易重用并提供更好的组织和结构，我们的应用程序。
还有更多的理解角度模块和@NgModule将在后面覆盖，但现在这已经足够上手。
