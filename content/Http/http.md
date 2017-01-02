#HTTP
为了从我们的Angular应用程序开始进行HTTP调用，我们需要导入`angular/http`模块并注如HTTP服务。 它通过`HttpModule`和`JsonpModule`实现XHR和JSONP请求。 在本节中，我们将只关注`HttpModule`。

#配置 angular/http
为了使用各种HTTP服务，我们需要在根`NgModule`的`imports`中加入`HttpModule`。这将允许我们从应用程序中的任何地方访问HTTP服务。

```typescript
...
import { MyApp } from './app.component'
import { HttpModule } from '@angular/http';

@NgModule({
  imports: [
    BrowserModule,
    ReactiveFormsModule,
    FormsModule,
    HttpModule
  ],
  providers: [SearchService],
  declarations: [MyApp],
  bootstrap: [MyApp]
})
export class MyAppModule {}
```
