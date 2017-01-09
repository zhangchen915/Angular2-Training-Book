#什么是Angular 2模块？

在Angular 2中，模块是以相关的组件，指令，管道和服务的组合的机制，其可以与其他模块组合以创建应用程序。 Angular 2应用程序可以被认为是一个谜题，需要每个部分（或每个模块）组合在一起才能够看到完整的图片。

另一个类比了解Angular 2模块的是类。 在类中，我们可以定义公共或私有方法。 公共方法是我们代码的其他部分可以用来与之交互的API，而私有方法是隐藏的实现细节。 以相同的方式，模块可以导出或隐藏组件，指令，管道和服务。 导出的元素可被其他模块使用，而未导出（隐藏）的元素只是在模块本身内部使用，并且不能被我们的应用程序的其他模块直接访问。

## 一个模块的基本使用

模块的基本使用 为了能够定义模块，我们必须使用装饰器 `NgModule`。

```typescript
import { NgModule } from '@angular/core';

@NgModule({
  imports: [ ... ],
  declarations: [ ... ],
  bootstrap: [ ... ]
})
export class AppModule { }		
```

在上面的例子中，我们已经将类`AppModule`转换为Angular 2模块，只需使用`NgModule`装饰器。 `NgModule`装饰器至少需要三个属性：`import`，`declarations`和`bootstrap`。

`imports` 属性需要一个模块数组。 这里是我们定义我们的拼图的部分（我们的应用程序）。 `declarations`属性需要一个作为模块的一部分的组件，指令和管道的数组。`bootstrap`属性是我们定义模块的根组件的地方。 即使这个属性也是一个数组，99％的情况我们只定义一个组件。

> 在非常特殊的情况下，可能需要多个组件来引导模块，但是我们不会在这里讨论这些边缘情况。

以下是由一个组件组成的基本模块的样子：

*app/app.component.ts*

```typescript
import { Component } from '@angular/core';

@Component({
  selector: 'app-root',
  template: '<h1>My Angular 2 App</h1>'
})
export class AppComponent {}
```

*app/app.module.ts*

```typescript
import { NgModule } from '@angular/core';
import { BrowserModule } from '@angular/platform-browser';

import { AppComponent } from './app.component';

@NgModule({
  imports: [BrowserModule],
  declarations: [AppComponent],
  bootstrap: [AppComponent]
})
export class AppModule { }
```

文件*app.component.ts*只是一个“hello world”组件，没有什么有趣的。另一方面，*app.module.ts*文件遵循我们之前定义模块的结构，但在这种情况下，我们定义了我们将要使用的模块和组件。

我们注意到的第一件事是，我们的模块是将`BrowserModule`导入为显式依赖项。 `BrowserModule`是一个内置的模块，用于导出基本指令，管道和服务。与以前版本的Angular 2不同，我们必须显式导入这些依赖关系，以便能够在模板中使用类似`*ngFor`或`*ngIf`的指令。

假定我们模块的根（唯一）组件是`AppComponent`，我们必须在`bootstrap`数组中列出它。因为在`declarations`属性中，我们应该定义组成应用程序的所有组件或管道，我们必须再次定义`AppComponent`。

在继续之前，有一个重要的澄清要做。**有两种类型的模块，根模块和功能模块。**

以同样的方式，在一个模块中，我们有一个根组件和许多可能的次级组件，**在应用程序中，我们只有一个根模块和零个或许多功能模块**。 为了能够引导我们的应用程序，Angular需要知道哪个是根模块。 识别根模块的简单方法是查看其`NgModule`装饰器的`imports`属性。 如果模块正在导入`BrowserModule`，那么它是一个根模块，如果相反是导入`CommonModule`，那么它是一个功能模块。

> 作为开发人员，我们需要注意在根模块中导入`BrowserModule`，而是将`CommonModule`导入我们为同一应用程序创建的任何其他模块。 如果不这样做，可能会导致在使用延迟加载模块时出现问题，我们将在以下部分中看到。

按照惯例，根模块应始终命名为`AppModule`。

## 引导应用程序

为了引导我们基于模块的应用程序，我们需要通知Angular哪一个是我们的根模块在浏览器中执行编译。这种在浏览器中的编译也称为“Just in Time”（JIT）编译。

*main.ts*

```typescript
import { platformBrowserDynamic } from '@angular/platform-browser-dynamic';
import { AppModule } from './app/app.module';

platformBrowserDynamic().bootstrapModule(AppModule);
```

> 还可以将编译作为我们工作流程的构建步骤来执行。 这种方法称为“Ahead of Time”（AOT）编译，并且将需要稍微不同的引导过程，我们将在另一部分中讨论。

[查看示例](https://plnkr.co/edit/4u6UKQuVSjHFeQYS993g?p=preview)

在下一节中，我们将了解如何创建具有多个组件，服务和管道的模块。