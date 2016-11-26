#捕获和释放
我们还可以选择使用.catch运算符。它允许我们捕获现有流上的错误，做一些事情然后传递异常。
```ts
import { Http } from '@angular/http';
import { Injectable } from '@angular/core';
import { Observable } from 'rxjs/Rx';

@Injectable()
export class SearchService {

  constructor(private http: Http) {}

  search(term: string) {
    return this.http.get('https://api.spotify.com/v1/dsds?q=' + term + '&type=artist')
      .map((response) => response.json())
      .catch((e) => {
        return Observable.throw(
          new Error(`${ e.status } ${ e.statusText }`)
        );
      });
  }
}
```
它还允许我们检查错误并决定采取哪条路由。例如，如果我们遇到服务器错误，则使用缓存版本的请求，否则重新抛出。
```ts
@Injectable()
export class SearchService {

  ...

  search(term: string) {
    return this.http.get('https://api.spotify.com/v1/dsds?q=' + term + '&type=artist')
      .map((response) => response.json())
      .catch((e) => {
        if (e.status >==  500) {
          return cachedVersion();
        } else {
          return Observable.throw(
            new Error(`${ e.status } ${ e.statusText }`)
          );
        }
      });
  }
}
```