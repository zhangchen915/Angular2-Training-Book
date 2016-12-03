#重试
有时您可能想要重试失败的请求。例如，如果用户离线，你可能需要每隔一段时间或不停地重试。

使用RxJS `retry`操作符。 它接受一个retryCount参数。 如果没有提供，它将无限期重试序列。
请注意，在重试阶段不会调用错误回调。 如果请求失败，它将被重试，并且只有在所有重试尝试失败之后，流才会抛出错误。
```ts
import { Http } from '@angular/http';
import { Injectable } from '@angular/core';
import { Observable } from 'rxjs/Rx';

@Injectable()
export class SearchService {

  constructor(private http: Http) {}

  search(term: string) {
    let tryCount = 0;
    return this.http.get('https://api.spotify.com/v1/dsds?q=' + term + '&type=artist')
      .map((response) => response.json())
      .retry(3);
  }
}
```

