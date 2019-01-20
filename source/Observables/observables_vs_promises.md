#Observables vs Promises
Promises和Observables都为我们提供了抽象，帮助我们处理应用程序的异步性质。 然而，两者之间存在重要的区别：

* 如前面的例子所示，Observables可以定义异步行为的建立和分解。
* Observables是可取消的
* 此外，可以使用API提供的重试操作符之一重试Observable，例如retry和retryWhen。 另一方面，Promises需要调用者访问返回promise的原始函数，以便具有重试能力。

⚡️Observables 就是 Promise 的超集