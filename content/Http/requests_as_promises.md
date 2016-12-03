#转换为Promises
Angular HTTP客户端返回的observable可以转换成promise。

> 我们建议在承诺上使用可观察量。 通过转换为promise，您将失去取消请求的能力和链接RxJS运算符的能力。

```ts
import { Http } from '@angular/http';
import { Injectable } from '@angular/core';
import 'rxjs/add/operator/map';
import 'rxjs/add/operator/toPromise';

@Injectable()
export class SearchService {

  constructor(private http: Http) {}

  search(term: string) {
    return this.http
      .get('https://api.spotify.com/v1/search?q=' + term + '&type=artist')
      .map((response) => response.json())
      .toPromise();
  }
}
```

然后我们将其作为组件中的常规Promise。
```ts
@Component({ /* ... */ })
export class MyApp {
    /* ... */

    search() {
        this.searchService.search(this.searchField.value)
          .then((result) => {
        this.result = result.artists.items;
      })
      .catch((error) => console.error(error));
    }
}
```
