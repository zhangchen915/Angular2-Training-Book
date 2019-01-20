# 延迟加载和依赖注入树

延迟加载模块在依赖注入（DI）树上创建自己的分支。 这意味着可能拥有属于延迟加载模块的服务，这些服务不能由根模块或我们的应用程序的任何其他热加载的模块访问。

为了显示这种行为，让我们继续上一节的示例，并向我们的`LazyModule`添加一个`CounterService`。

*app/lazy/lazy.module.ts*

```typescript
...
import { CounterService } from './counter.service';

@NgModule({
  ...
  providers: [CounterService]
})
export class LazyModule {}
```

这里我们将`CounterService`添加到`providers`数组中。 我们的`CounterService`是一个简单的类，它保存对计数器属性的引用。

*app/lazy/counter.service.ts*

```typescript
import { Injectable } from '@angular/core';

@Injectable()
export class CounterService {
  counter = 0;
}
```

我们可以修改`LazyComponent`来使用这个服务，使用一个按钮来增加计数器属性。

*app/lazy/lazy.component.ts*

```typescript
import { Component } from '@angular/core';

import { CounterService } from './counter.service';

@Component({
  template: `
    <p>Lazy Component</p>
    <button (click)="increaseCounter()">Increase Counter</button>
    <p>Counter: {{ counterService.counter }}</p>
  `
})
export class LazyComponent {

  constructor(public counterService: CounterService) {}

  increaseCounter() {
    this.counterService.counter += 1;
  }
}
```

[View Example](https://plnkr.co/edit/RUp3QhHWmxBIQQAAw2im?p=preview)

服务可以工作了。 如果我们递增计数器，然后在`eager`和 `lazy`路由之间来回导航，`counter`值将在延迟加载模块中持续。

但问题是，我们如何验证服务是隔离的，不能在属于不同模块的组件中使用？ 让我们尝试在`EagerComponent`中使用相同的服务。

*app/eager.component.ts*

```typescript
import { Component } from '@angular/core';
import { CounterService } from './lazy/counter.service';

@Component({
  template: `
    <p>Eager Component</p>
    <button (click)="increaseCounter()">Increase Counter</button>
    <p>Counter: {{ counterService.counter }}</p>
  `
})
export class EagerComponent {
  constructor(public counterService: CounterService) {}

  increaseCounter() {
    this.counterService.counter += 1;
  }
}
```

如果我们尝试运行这个新版本的代码，我们将在浏览器控制台中收到一条错误消息：

```
No provider for CounterService!
```

这个错误告诉我们，定义了`EagerComponent`的`AppModule`不知道叫做`CounterService`的服务。当`CounterService`在浏览器中延迟加载时，它驻留在为`LazyModule`创建的DI树的不同分支中。