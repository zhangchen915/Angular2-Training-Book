# 共享模块和依赖注入

现在我们已经证明延迟加载模块在依赖注入树上创建自己的分支，我们需要学习如何处理通过在 `eager` 和`lazy` 加载模块中的共享模块导入的服务。

让我们创建一个名为SharedModule的新模块，并在那里定义CounterService。

*app/shared/shared.module.ts*

```
import { NgModule } from '@angular/core';
import { CounterService } from './counter.service';

@NgModule({
  providers: [CounterService]
})
export class SharedModule {}

```

Now we are going to import that `SharedModule` in the `AppModule` and the `LazyModule`.

*app/app.module.ts*

```
...
import { SharedModule } from './shared/shared.module';

@NgModule({
  imports: [
    SharedModule,
    ...
  ],
  declarations: [
    EagerComponent,
    ...
  ]
  ...
})
export class AppModule {}

```

*app/lazy/lazy.module.ts*

```
...
import { SharedModule } from '../shared/shared.module';

@NgModule({
  imports: [
    SharedModule,
    ...
  ],
  declarations: [LazyComponent]
})
export class LazyModule {}

```

With this configuration, the components of both modules will have access to the `CounterService`. We are going to use this service in `EagerComponent` and `LazyComponent` in exactly the same way. Just a button to increase the internal `counter` property of the service.

*app/eager.component.ts*

```
import { Component } from '@angular/core';
import { CounterService } from './shared/counter.service';

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

[View Example](https://plnkr.co/edit/7evZh7XMUxf9HPPKdqYa?p=preview)

如果看过了上面的在线示例，你会注意到计数器似乎在`EagerComponent`和`LazyComponent`中独立行为，我们可以增加一个计数器的值，而不改变另一个。 换句话说，我们最终得到了两个`CounterService`实例，一个存在于`AppModule`的DI树根目录中，另一个存在于`LazyModule`可访问的DI树的较低分支中。

这不是必要的错误，你可能会发现，你可能需要不同的实例相同的服务，但我打赌大部分时间，这不是你想要的。 考虑认证服务的例子，你需要具有相同的信息的相同实例在任何地方都可以忽略，如果我们在一个急切或延迟加载模块中使用服务。

在下一节中，我们将学习如何只有一个共享服务实例。