#引导Providers

引导进程还在Angular中启动依赖注入系统。 我们不会在这里讨论Angular的依赖注入系统 - 稍后会介绍。 相反，让我们来看看如何使用整个应用的Providers引导应用程序的示例。

为此，我们将使用我们用来引导应用程序的模块的`providers`属性注册一个名为`GreeterService`的服务。

*app/app.module.ts*

```typescript
import { BrowserModule }  from '@angular/platform-browser';
import { NgModule } '@angular/core';
import { AppComponent } from './app.component'
import { GreeterService } from './greeter.service';

@NgModule({
  imports: [BrowserModule],
  providers: [GreeterService],
  declarations: [AppComponent],
  bootstrap: [AppComponent]
})
export class AppModule { }
```
[View Example](https://embed.plnkr.co/W8CkQQ62pIjHFr7BqVjX/)