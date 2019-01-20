# Angular 2's DI
最后一个例子介绍了一个假设的`Injector`对象。Angular 2更进一步简化了DI。 使用Angular 2，程序员几乎不必陷入注入细节。

Angular 2的DI系统（大部分）通过`@NgModule`来控制。 具体来说是`providers` 和`declarations`数组。 （`declarations`是我们放置组件，管道和指令的地方；`providers` 是我们提供服务的地方）

例如：

```typescript
import { Injectable, NgModule } from '@angular/core';

@Component({...})
class ChatWidget {
  constructor(private authService: AuthService, private authWidget: AuthWidget,
    private chatSocket: ChatSocket) {}
}

@NgModule({
  declarations: [ ChatWidget ]
})
export class AppModule {};
```
在上面的例子中，`AppModule`被告知关于`ChatWidget`类。 另一种说法是，Angular 2已经提供了一个`ChatWidget`。

这看起来很简单，但读者会想知道Angular 2如何知道如何构建`ChatWidget` 。如果`ChatWidget`是一个字符串或一个简单的函数怎么办？

Angular 2假设它被赋予一个类。

`AuthService` ，`AuthWidget`和`ChatSocket`怎么样？ `ChatWidget`如何获取这些？

这不是，至少还没有。 Angular 2不知道他们。 这可以很容易改变：

```typescript
import { Injectable, NgModule } from '@angular/core';

@Component({...})
class ChatWidget {
  constructor(private authService: AuthService, private authWidget: AuthWidget,
    private chatSocket: ChatSocket) {}
}

@Component({...})
class AuthWidget {}

@Injectable()
class AuthService {}

@Injectable()
class ChatSocket {}

@NgModule({
  declarations[ ChatWidget, AuthWidget ]
  providers: [ AuthService, ChatSocket ],
})		
```
好吧，这开始看起更完整一些了。虽然还不清楚`ChatWidget`如何被告知关于它的依赖。也许这与那些奇怪的`@Injectable`语句有关。