#引导提供程序

引导进程也启动Angular 2中的依赖注入系统。我们不会在这里讨论Angular 2的依赖注入系统 - 稍后会介绍。相反，让我们来看看如何使用应用程序范围的提供程序引导应用程序的示例。
对于这一点，我们将注册一个叫做服务`Greeter`与`providers`我们使用来引导该应用程序的模块的属性。

*app/app.module.ts*

```typescript
app / app.module.ts
import { BrowserModule }  from '@angular/platform-browser';
import { NgModule } '@angular/core';
import { MyApp } from './app.component'
import { Greeter } from './greeter.service';

@NgModule({
  imports: [BrowserModule],
  providers: [Greeter],
  declarations: [MyApp],
  bootstrap: [MyApp]
})
export class MyAppModule {

}
```
[View Example](https://plnkr.co/edit/jty1DE?p=preview)