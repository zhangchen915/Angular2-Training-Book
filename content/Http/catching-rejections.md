#捕获异常
要捕获拒绝，我们使用订阅者的 `error` 和 `complete`的回调。
```typescript
import { HttpClient } from "@angular/common/http";
import { Injectable } from '@angular/core';

@Injectable()
export class AuthService {

  constructor(private http: HttpClient) {}

  login(username, password) {
    const payload = {
      username: username,
      password: password
    };

    this.http.post(`${ BASE_URL }/auth/login`, payload)
      .catch( error => {
            console.error("error catched", error);
            return Observable.of({description: "Error Value Emitted"});
        })
      .subscribe(
        authData => this.storeToken(authData.id_token),
        (err) => console.error(err),
        () => console.log('Authentication Complete')
      );
  }
}
```
