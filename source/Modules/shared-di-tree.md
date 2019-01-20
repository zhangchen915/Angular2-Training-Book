# 共享相同的依赖注入树

到目前为止，我们的问题是我们在DI树的不同级别创建相同服务的两个实例。 在树的较低分支中创建的实例会遮蔽在根级别定义的实例。 解决方案？ 避免在延迟加载模块的DI树的较低级别中创建第二个实例，并且仅使用在树根处注册的服务实例。

为了实现这一点，我们需要修改`SharedModule`的定义，而不是在`providers`属性中定义我们的服务，我们需要创建一个称为`forRoot`的静态方法，该方法将与模块本身一起导出服务。

*app/shared/shared.module.ts*

```typescript
import { NgModule, ModuleWithProviders } from '@angular/core';
import { CounterService } from './counter.service';

@NgModule({})
export class SharedModule {
  static forRoot(): ModuleWithProviders {
    return {
      ngModule: SharedModule,
      providers: [CounterService]
    };
  }
}
```

通过这个设置，我们可以在我们的根模块`AppModule`中导入这个模块，调用`forRoot`方法来注册模块和服务。

*app/app.module.ts*

```typescript
...
import { SharedModule } from './shared/shared.module';

@NgModule({
  imports: [
    SharedModule.forRoot(),
    ...
  ],
  ...
})
export class AppModule {}
```

相反，当在`LazyModule`中导入相同的模块时，我们不会调用`forRoot`方法，因为我们不想在DI树的不同级别注册该服务，因此`LazyModule`的声明不会改变。

*app/lazy/lazy.module.ts*

```typescript
...
import { SharedModule } from '../shared/shared.module';

@NgModule({
  imports: [
    SharedModule,
    ...
  ],
  ...
})
export class LazyModule {}
```

[View Example](https://plnkr.co/edit/xz5wZvqQvzdD0uOZYXg4?p=preview)

这次，只要我们更改`counter` 属性的值，该值就会在`EagerComponent`和`LazyComponent`之间共享，证明我们正在使用`CounterService`的同一个实例。