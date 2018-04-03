#进行HTTP请求
要发出HTTP请求，我们将使用Http服务。在本示例中，我们创建了一个`SearchService`以与Spotify API进行交互。

我们通过[HttpHeaders](https://angular.io/api/common/http/HttpHeaders)设置HTTP头，HttpParams设置请求参数

```typescript
import { HttpClient, HttpParams } from "@angular/common/http";
import { Injectable } from '@angular/core';
import 'rxjs/add/operator/map';

@Injectable()
export class SearchService {
  constructor(private http: HttpClient) {}

  search(term: string) {
    const params = new HttpParams()
                    .set('q', term)
                    .set('type', "artist");
    
    return this.http
      .get('https://api.spotify.com/v1/search',{params})
      .map()
      .subscribe()
  }
}
```
这里我们做一个HTTP GET请求，它作为一个可观察者暴露给我们。 你会注意到`.map`操作符被链接到`.subscribe`。 

除了`get`，还有`post`, `put`,` patch`和`delete`方法，他们都返回observables。
