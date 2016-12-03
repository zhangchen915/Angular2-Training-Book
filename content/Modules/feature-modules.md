#  创建功能模块

当我们的根模块开始增长时，一些元素（组件，指令等）开始明显地以某种方式相关，几乎成了可以“插入”的库。
在我们前面的例子中，我们开始看到了。 我们的根模块有一个组件，一个管道和一个服务，其唯一的目的是处理信用卡。 如果我们将这三个元素提取到自己的**特征模块**，然后将它们导入我们的**根模块**怎么办？

我们将这样做。第一步是创建两个文件夹以区分属于根模块的元素和属于要素模块的元素。

```
.
├── app
│   ├── app.component.ts
│   └── app.module.ts
├── credit-card
│   ├── credit-card-mask.pipe.ts
│   ├── credit-card.component.ts
│   ├── credit-card.module.ts
│   └── credit-card.service.ts
├── index.html
└── main.ts

```

注意每个模块文件夹下的模块文件: *app.module.ts* and *credit-card.module.ts*.。让我们先关注后者。

*credit-card/credit-card.module.ts*

```typescript
import { NgModule } from '@angular/core';
import { CommonModule } from '@angular/common';

import { CreditCardMaskPipe } from './credit-card-mask.pipe';
import { CreditCardService } from './credit-card.service';
import { CreditCardComponent } from './credit-card.component';

@NgModule({
  imports: [CommonModule],
  declarations: [
    CreditCardMaskPipe,
    CreditCardComponent
  ],
  providers: [CreditCardService],
  exports: [CreditCardComponent]
})
export class CreditCardModule {}
```

 `CreditCardModule` 和 `AppModule` 很像，但是有几个很重要的区别：

- 我们不导入`BrowserModule`，而是导入`CommonModule`。 如果我们在这里看到`BrowserModule`的文档，我们可以看到它是重新导出`CommonModule`与许多其他服务，有助于在浏览器中呈现Angular 2应用程序。 这些服务将我们的根模块与特定平台（浏览器）耦合，但我们希望我们的特性模块与平台无关。 这就是为什么我们只导入`CommonModule，`它只导出公共指令和管道。

> 当涉及到组件，管道和指令时，每个模块都应该导入自己的依赖关系，而忽略在根模块或任何其他功能模块中导入相同的依赖关系。 总之，即使有多个特征模块，每个特征模块都需要导入`CommonModule`。

- 我们使用一个名为`exports`的新属性。 默认情况下，声明数组中定义的每个元素都是**私有的**。 我们应该只导出应用程序中其他模块需要执行的工作。 在我们的例子中，我们只需要使`CreditCardComponent`可见，因为它在`AppComponent`的模板中使用。

*app/app.component.ts*

```typescript
...
@Component({
  ...
  template: `
    ...
    <rio-credit-card></rio-credit-card>
  `
})
export class AppComponent {}
```

> 我们保持`CreditCardMaskPipe`是私有的，因为它只在`CreditCardModule`中使用，没有其他模块应该直接使用它。

我们现在可以将此功能模块导入到我们的简化根模块中。

*app/app.module.ts*

```
import { NgModule } from '@angular/core';
import { BrowserModule } from '@angular/platform-browser';

import { CreditCardModule } from '../credit-card/credit-card.module';
import { AppComponent } from './app.component';

@NgModule({
  imports: [
    BrowserModule,
    CreditCardModule
  ],
  declarations: [AppComponent],
  bootstrap: [AppComponent]
})
export class AppModule { }

```

在这一点上，我们完成了，我们的应用程序按预期运行。

[View Example](https://plnkr.co/edit/0j1jS5PIHI8MAbZjECnK?p=preview)

## 服务和懒加载

这里是Angular模块的棘手部分。 除非明确导出组件，管道和指令的范围限于其模块，否则服务全局可用，除非模块延迟加载。
很难理解，首先，让我们试着看看在我们的例子中`CreditCardService`发生了什么。 首先请注意，服务不在`exports`数组中，而是在`providers`数组中。 有了这个配置，我们的服务将随处可用，即使在`AppComponent`，它居住在另一个模块。 所以，即使使用模块，没有办法有一个“私人”服务，除非...模块正在懒加载。
当模块被延迟加载时，Angular将创建一个子注入器（它是根模块的根注入器的一个子进程），并将在那里创建一个服务实例。

想象一下，我们的CreditCardModule配置为延迟加载。 使用我们当前的配置，当应用程序被引导并且我们的根模块被加载到内存中时，CreditCardService（一个单例）的一个实例将被添加到根注入器。 但是，当CreditCardModule在未来的某个时间被延迟加载时，将使用CreditCardService的新实例为该模块创建子注入器。 在这一点上，我们有一个分层注入器，具有相同服务的两个实例，这通常不是我们想要的。

考虑进行认证的服务的示例。 我们想在整个应用程序中只有一个单例，忽略我们的模块是在引导或延迟加载时加载。 因此，为了使我们的特性模块的服务**仅**添加到根注入器，我们需要使用不同的方法。

*credit-card/credit-card.module.ts*

```
import { NgModule, ModuleWithProviders } from '@angular/core';
/* ...other imports... */

@NgModule({
  imports: [CommonModule],
  declarations: [
    CreditCardMaskPipe,
    CreditCardComponent
  ],
  exports: [CreditCardComponent]
})
export class CreditCardModule {
  static forRoot(): ModuleWithProviders {
    return {
      ngModule: CreditCardModule,
      providers: [CreditCardService]
    }
  }
}
```

与以前不同，我们不是将我们的服务直接放在NgModule装饰器的属性提供者。 这次我们定义一个称为forRoot的静态方法，其中我们定义模块和要导出的服务。

有了这个新的语法，我们的根模块略有不同。

*app/app.module.ts*

```
/* ...imports... */

@NgModule({
  imports: [
    BrowserModule,
    CreditCardModule.forRoot()
  ],
  declarations: [AppComponent],
  bootstrap: [AppComponent]
})
export class AppModule { }

```

你能发现差别吗？ 我们不直接导入CreditCardModule，而是我们正在导入的是从forRoot方法返回的对象，其中包括CreditCardService。 尽管这种语法比原始语法更复杂，但它将保证我们只有一个CreditCardService实例被添加到根模块。 当CreditCardModule被加载（即使是延迟加载）时，该服务的新实例不会被添加到子注入器。

[View Example](https://plnkr.co/edit/nnyE8L4ciKCL2uBruM12?p=preview)

作为经验法则，在从功能模块导出服务时，**始终使用forRoot语法**，除非您有非常特殊的需求，需要在依赖注入树的不同级别有多个实例。