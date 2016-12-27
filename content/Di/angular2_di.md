# Angular 2's DI
最后一个例子介绍了一个假设的Injector对象。 角度2更进一步简化了DI。 使用Angular 2，程序员几乎不必陷入注入细节。

Angular 2的DI系统（大部分）通过@NgMule来控制。 具体来说是提供者的数组。
例如：
```ts
import { Injectable, NgModule } from '@angular/core';

@Injectable()
class Hamburger {
  constructor(private bun: Bun, private patty: Patty,
    private toppings: Toppings) {}
}

@NgModule({
  providers: [ Hamburger ],
})
export class DiExample {};
```
在上面的例子中，DiExample模块被告知关于Hamburger类。
另一种说法是，Angular 2已经提供了一个汉堡包。
这看起来很简单，但精明的读者会想知道Angular 2如何知道如何构建汉堡包。 如果汉堡是一个字符串，或一个简单的函数？
Angular 2假设它被赋予一个类。
Bun，Patty和浇头怎么样？ 汉堡如何得到这些？
这不是，至少还没有。 Angular 2不知道他们。 这可以很容易改变足够：
```ts
import { Injectable, NgModule } from '@angular/core';

@Injectable()
class Hamburger {
  constructor(private bun: Bun, private patty: Patty,
    private toppings: Toppings) {}
}

@Injectable()
class Patty {}

@Injectable()
class Bun {}

@Injectable()
class Toppings {}

@NgModule({
  providers: [ Hamburger, Patty, Bun, Toppings ],
})
```
好吧，这开始看起来有点更完整。 虽然仍然不清楚如何告诉汉堡包其依赖。 也许这与那些奇怪的@Injectable语句有关。