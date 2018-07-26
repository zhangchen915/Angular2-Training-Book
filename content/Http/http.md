#HTTP
为了从我们的Angular应用程序开始进行HTTP调用，我们需要从`@angular/common/http`中导入 HTTP 模块。 

```typescript
import {HttpClientModule} from '@angular/common/http';

@NgModule({
    declarations: [
        AppComponent
    ],
    imports: [
        BrowserModule,
        HttpClientModule
    ],
    providers: [],
    bootstrap: [AppComponent]
})
export class AppModule {}
```

⚡️HttpClient 是 Angular2 HTTP 的升级版，在4.3版本引入。默认解析JSON，所以我们不需要再进行显式的解析。即我们不需要再使用以下代码：

```typescript
http.get(url).map(res => res.json()).subscribe(...)
```

