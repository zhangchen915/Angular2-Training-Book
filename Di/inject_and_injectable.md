#@Inject 和 @Injectable
看起来像@SomeName的语句是装饰器。 装饰器是JavaScript的扩展。 简而言之，装饰器让程序员修改和/或标记方法，类，属性和参数。 有很多装饰师。 在本节中，重点将放在与DI相关的装饰器：@Inject和@Injectable。 有关装饰器的更多信息，请参阅EcmaScript 6和TypeScript特性部分。

#@Inject() 
`@Inject()`是一个手动机制，让Angular 2知道必须注入参数。 它可以这样使用：
```ts
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
在上面，我们通过调用@Inject（Hamburger）要求h作为单例类型的Angular与类标记Hamburger关联。 重要的是要注意，我们使用汉堡包作为其打字和作为其单例的引用。 我们不使用汉堡实例化任何东西，Angular为我们在幕后。
当使用TypeScript时，@Inject只需要注入基元。 TypeScript的类型让Angular 2知道在大多数情况下要做什么。 以上示例将在TypeScript中简化为：
```ts
import { Component } from '@angular/core';
import { Hamburger } from '../services/hamburger';

@Component({
  selector: 'app',
  template: `Bun Type: {{ bunType }}`
})
export class App {
  bunType: string;
  constructor(h: Hamburger) {
    this.bunType = h.bun.type;
  }
}
```

#@Injectable()
