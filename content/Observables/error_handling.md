#错误处理
如果出现意外事件，我们可以在Observable流上引发一个错误，并在订阅例程中使用保留用于处理错误的函数来查看发生了什么。
```ts
export class App {

    values: number[] = [];
    anyErrors: Error;
    private data: Observable<number[]>;

    constructor() {

        this.data = new Observable(observer => {
              setTimeout(() => {
                observer.next(10);
            }, 1500);
            setTimeout(() => {
                observer.error(new Error('Something bad happened!'));
            }, 2000);
            setTimeout(() => {
                observer.next(50);
            }, 2500);
        });

        let subscription = this.data.subscribe(
            value => this.values.push(value),
            error => this.anyErrors = error
        );
    }
}
```
这里出现一个错误并捕获。 需要注意的一点是，如果我们在引发错误后包含一个`.complete()`，这个事件不会真正触发。 因此，您应该记住在错误处理程序中包含一些调用，这将关闭应用程序中的任何可视加载状态。