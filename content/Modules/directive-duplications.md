# 指令复制

因为我们不再直接在需要它的每个组件中定义每个组件和指令，所以我们需要知道Angular模块如何处理指向同一个元素（具有相同的选择器）的指令和组件。
让我们假设一下，我们错误地创建了两个针对同一个属性的指令：

> 这个例子是在[官方文档](https://angular.io/docs/ts/latest/guide/ngmodule.html#!#resolve-conflicts)中找到的代码的变体。

*blue-highlight.directive.ts*

```typescript
import { Directive, ElementRef, Renderer } from '@angular/core';

@Directive({
  selector: '[appHighlight]'
})
export class BlueHighlightDirective {
  constructor(renderer: Renderer, el: ElementRef) {
    renderer.setElementStyle(el.nativeElement, 'backgroundColor', 'blue');
    renderer.setElementStyle(el.nativeElement, 'color', 'gray');
  }
}
```

*yellow-highlight.directive.ts*

```typescript
import { Directive, ElementRef, Renderer } from '@angular/core';

@Directive({
  selector: '[appHighlight]'
})
export class YellowHighlightDirective {
  constructor(renderer: Renderer, el: ElementRef) {
    renderer.setElementStyle(el.nativeElement, 'backgroundColor', 'yellow');
  }
}
```

这两个指令是相似的，他们试图对一个元素进行风格化。 `BlueHighlightDirective`将尝试将元素的背景颜色设置为蓝色，同时将文本的颜色更改为灰色，而`YellowHighlightDirective`将尝试仅将背景颜色更改为黄色。 请注意，两者都定位到具有属性突出显示的任何HTML元素。 如果我们将两个指令添加到同一个模块会发生什么？

*app.module.ts*

```typescript
// Imports

@NgModule({
  imports: [BrowserModule],
  declarations: [
    AppComponent,
    BlueHighlightDirective,
    YellowHighlightDirective
  ],
  bootstrap: [AppComponent]
})
export class AppModule { }
```

让我们看看我们如何在模块的唯一组件中使用它。

*app.component.ts*

```typescript
import { Component } from "@angular/core";

@Component({
  selector: 'rio-app',
  template: '<h1 highlight>My Angular 2 App</h1>'
})
export class AppComponent {}
```

我们可以看到，在我们的组件模板中，我们在`h1`元素中使用了`appHighlight`指令，但是哪些风格最终会被应用？ 答案是：文本将是灰色的，背景是黄色的。

[View Example](https://plnkr.co/edit/vwfSCpqOspwjICC6eKY2?p=preview)

我们允许将多个指令用在同一模块中的相同元素上。Angular将**按顺序**进行每个转换每个指令。

```typescript
declarations: [
  ...,
  BlueHighlightDirective,
  YellowHighlightDirective
]
```

因为我们已经在数组中定义了两个指令，并且数组是**有序集合的项**，当编译器找到具有`appHighlight`属性的元素时，它将首先应用`BlueHighlightDirective`的变换，将文本设置为灰色，将背景设置为蓝色，然后 应用`YellowHighlightDirective`的变换，再次将背景颜色更改为黄色。
总之，**当两个或多个指令定位同一个元素时，它们将按照它们定义的顺序应用**。