#@Inject 和 @Injectable
看起来像`@SomeName`的语句是装饰器。  [装饰器](http://blog.wolksoftware.com/decorators-reflection-javascript-typescript) 是JavaScript的扩展。 简而言之，装饰器让程序员修改和/或标记方法，类，属性和参数。有很多种装饰器。 在本节中，重点将放在与DI相关的装饰器：`@Inject`和`@Injectable`。 有关装饰器的更多信息，请参阅[EcmaScript 6和TypeScript特性部分](https://angular-2-training-book.rangle.io/handout/features/) 。

#@Inject() 
`@Inject()`是一个手动机制，让Angular 2知道必须注入参数。 它可以这样使用：
```typescript
import { Component, Inject } from '@angular/core';
import { Hamburger } from '../services/hamburger';

@Component({
  selector: 'app',
  template: `Bun Type: {{ bunType }}`
})
export class App {
  bunType: string;
  constructor(@Inject(Hamburger) h) {
    this.bunType = h.bun.type;
  }
}
```
在上面示例中，我们要求`chatWidget`是单例的，Angular通过调用`@Inject(ChatWidget)`与类符号`ChatWidget`关联。 需要特别注意的是，我们使用`ChatWidget`的类型和作为其单例的引用。 我们没有使用`ChatWidget`来实例化任何东西，Angular在幕后帮我们做好了。

当使用TypeScript时，@Inject只需要注入*primitives*。 TypeScript的类型让Angular 2知道在大多数情况下要做什么。 以上示例将在TypeScript中简化为：

```typescript
import { Component } from '@angular/core';
import { ChatWidget } from '../components/chat-widget';

@Component({
  selector: 'app',
  template: `Encryption: {{ encryption }}`
})
export class App {
  encryption: boolean;
  constructor(chatWidget: ChatWidget) {
    this.encryption = chatWidget.chatSocket.encryption;
  }
}
```

[View Example](https://plnkr.co/edit/xLQ0btl5t79jLqOqobFm?p=preview)

#@Injectable()

`@Injectable()`让Angular 2知道一个类可以用于依赖注入器。 如果类上有其他Angular 2装饰器或没有任何依赖，`@Injectable()`不是必须的。

重要的是任何要注入Angular 2的类都被*装饰*。 然而，最佳实践是使用`@Injectable()`来装饰注入，因为它对开发者更强的语义。

这里有一个用`@Injectable`标记的 `ChatWidget`示例：

```typescript
import {Injectable} from '@angular/core';
import {AuthService} from './auth-service';
import {AuthWidget} from './auth-widget';
import {ChatSocket} from './chat-socket';

@Injectable()
export class ChatWidget {
  constructor(public authService: AuthService, public authWidget: AuthWidget, public chatSocket: ChatSocket) {
  }
}
```
在上面的例子中，Angular 2的注入器通过使用类型信息来确定要注入到`ChatWidget`的构造函数中。 这是可能的，因为这些特定的依赖关系是类型化的，并且不是原始类型。 在某些情况下，Angular 2的DI需要更多的信息，而不仅仅是类型。