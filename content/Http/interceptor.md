# Http 拦截器

大多数拦截器检查请求的方式，并将（可能已更改的）请求转发给实现接口`handle()`的`next`对象的方法[`HttpHandler`](https://angular.io/api/common/http/HttpHandler)。

```typescript
import {Injectable} from "@angular/core";
import {HttpEvent, HttpHandler, HttpInterceptor} from "@angular/common/http";
import {HttpRequest} from "@angular/common/http";
import {Observable} from "rxjs/Observable";

@Injectable()
export class AuthInterceptor implements HttpInterceptor {
    
    constructor(private authService: AuthService) {
    }

    intercept(req: HttpRequest<any>, next: HttpHandler): Observable<HttpEvent<any>> {
        const clonedRequest = req.clone({
            headers: req.headers.set('X-CustomAuthHeader', authService.getToken())
        });
        console.log("new headers", clonedRequest.headers.keys());
        return next.handle(clonedRequest);
    }
}
```

#### 配置拦截器

```typescript
@NgModule({
    declarations: [
        AppComponent
    ],
    imports: [
        BrowserModule,
        HttpClientModule
    ],
    providers: [
        [ { provide: HTTP_INTERCEPTORS, useClass: AuthInterceptor, multi: true } ]
    ],
    bootstrap: [AppComponent]
})
export class AppModule { }
```

一个改变HTTP头的拦截器可以用于许多不同的操作，包括：

- 认证/授权
- 缓存行为; 例如，`If-Modified-Since`
- XSRF保护