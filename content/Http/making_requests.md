#进行HTTP请求
要发出HTTP请求，我们将使用Http服务。在本示例中，我们创建了一个`SearchService`以与Spotify API进行交互。
```typescript
import { Http } from '@angular/http';
import { Injectable } from '@angular/core';
import 'rxjs/add/operator/map';

@Injectable()
export class SearchService {

  constructor(private http: Http) {}

  search(term: string) {
    return this.http
      .get('https://api.spotify.com/v1/search?q=' + term + '&type=artist')
      .map((response) => response.json());
  }
}
```
这里我们做一个HTTP GET请求，它作为一个可观察者暴露给我们。 你会注意到`.map`操作符被链接到`.get`。 Http服务为我们提供了作为字符串的原始响应。 为了使用获取的数据，我们必须将其转换为JSON。

除了`Http.get()`，还有`Http.post()`, `Http.put()`, `Http.delete()`等等。他们都返回observables。