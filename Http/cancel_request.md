#取消请求
取消HTTP请求是常见的要求。 例如，您可以有一个请求队列，其中一个新请求取代一个待处理请求，并且该待处理请求需要取消。

要取消请求，我们称其订阅的`unsubscribe`函数。
```ts
@Component({ /* ... */ })
export class MyApp {
    /* ... */

    search() {
        const request = this.searchService.search(this.searchField.value)
          .subscribe(
              (result) => { this.result = result.artists.items; },
              (err) => { this.errorMessage = err.message; },
              () => { console.log('Completed'); }
          );

        request.unsubscribe();
    }
}
```
