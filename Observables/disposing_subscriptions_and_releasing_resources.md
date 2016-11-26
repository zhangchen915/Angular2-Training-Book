#处理订阅和释放资源
在某些情况下，我们可能需要取消订阅Observable流。这是非常简单的，因为`.subscribe()`调用返回一个数据类型，我们可以调用`unsubscribe()`。
```ts
export class MyApp {

  private data: Observable<Array<string>>;
  private value: string;
  private subscribed: boolean;
  private status: string;

    init() {

        this.data = new Observable(observer => {
            let timeoutId = setTimeout(() => {
                observer.next('You will never see this message');
            }, 2000);

            this.status = 'Started';

            return onUnsubscribe = () => {
                this.subscribed = false;
                this.status = 'Finished';
                clearTimeout(timeoutId);
            }
        });

        let subscription = this.data.subscribe(
            value => this.value = value,
            error => console.log(error),
            () => this.status = 'Finished';
        );
        this.subscribed = true;

        setTimeout(() => {
          subscription.unsubscribe();
        }, 1000);
    }

}
```
调用.unsubscribe（）将取消一个成员的回调监听Observable流。 当创建Observable时，您还可以返回自定义回调onUnsubscribe，当收听流的成员取消订阅时将调用该回调。 这对于必须实现的任何类型的清理都很有用。 如果我们没有清除setTimeout，那么值仍然会发射，但是没有人听。 为了节省资源，我们应该停止发射值。 一个重要的事情要注意的是，当您调用.unsubscribe（）时，您正在销毁正在侦听的订阅对象，因此附加到该订阅对象的完成事件将不会被调用。
在大多数情况下，我们不需要显式调用unsubscribe方法，除非我们想提前取消或我们的Observable的生命周期比我们的订阅更长。 Observable运算符的默认行为是在发布.complete（）或.error（）消息后立即处理订阅。 请记住，RxJS的设计是在大多数时候以“发射和忘记”的方式使用。