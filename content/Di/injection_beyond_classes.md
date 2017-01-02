# 类以外的注入

到目前为止，注入过的唯一类型是类，但Angular 2不限于注入类。 还简要提及了 `providers` 的概念。

到目前为止， `providers` 已经在数组中使用Angular 2的`@NgModule`元数据。 `providers` 也都是类标识符。 Angular 2让程序员用更详细的“食谱”指定 `providers` 。 这是通过为提供Angular 2一个对象字面量（`{}`）实现的：

```typescript
import { NgModule } from '@angular/core';
import { App } from './containers/app'; // hypothetical app component
import { ChatWidget } from './components/chat-widget';

@NgModule({
  providers: [ { provide: ChatWidget, useClass: ChatWidget } ],
})
export class DiExample {};
```

这个例子是另一个`provide`一个类的例子，但它使用Angular 2的更长格式。

这个长格式很方便。 如果程序员想要关闭`ChatWidget`实现，例如允许一个`MockChatWidget`，他们可以轻松地做到：

```typescript
import { NgModule } from '@angular/core';
import { App } from './containers/app'; // hypothetical app component
import { ChatWidget } from './components/chat-widget';
import { MockChatWidget } from './components/mock-chat-widget';

@NgModule({
  providers: [ { provide: ChatWidget, useClass: MockChatWidget } ],
})
export class DiExample {};
```

这个实现交换的最好的部分是注入系统知道如何构建`MockChatWidget`，并将排序所有`provider` 。

注射器可以使用多个类。 `useValue`和`useFactory`是Angular 2可以使用的 `provider` “recipes”的另外两个示例。 例如：

```typescript
import { NgModule } from '@angular/core';
import { App } from './containers/app'; // hypothetical app component

const randomFactory = () => { return Math.random(); };

@NgModule({
  providers: [ { provide: 'Random', useFactory: randomFactory } ],
})
export class DiExample {};
```

在假设的app组件中，“Random”可以注入：

```typescript
import { Component, Inject, provide } from '@angular/core';

@Component({
  selector: 'app',
  template: `Random: {{ value }}`
})
export class App {
  value: number;
  constructor(@Inject('Random') r) {
    this.value = r;
  }
}
```

[View Example](http://plnkr.co/edit/Dkm0cJF80EdmPcWZx45W?p=preview)

One important note is that 'Random' is in quotes, both in the `provide` function and in the consumer. This is because as a factory we have no `Random` identifier anywhere to access.

The above example uses Angular 2's `useFactory` recipe. When Angular 2 is told to `provide` things using `useFactory`, Angular 2 expects the provided value to be a function. Sometimes functions and classes are even more than what's needed. Angular 2 has a "recipe" called `useValue` for these cases that works almost exactly the same:

一个重要的注意事项是，“Random”在报价中，在`provide` 功能和消费者。 这是因为作为一个工厂，我们没有在任何地方访问`Random`标识符。

上面的例子使用Angular 2的`useFactory`。 当Angular 2被告知使用`useFactory `来`provide` 东西时，Angular 2期望提供的值是一个函数。 有时函数和类甚至比需要的更多。 Angular 2有一个名为`useValue`的“食谱”，这些情况几乎完全相同：

```typescript
import { NgModule } from '@angular/core';
import { App } from './containers/app'; // hypothetical app component

@NgModule({
  providers: [ { provide: 'Random', useValue: Math.random() } ],
})
export class DiExample {};
```

[View Example](http://plnkr.co/edit/63GsCDOElY7J8LNAbTjL?p=preview)

在这种情况下，`Math.random`的乘积被分配给传递给 `provider`的`useValue`属性。

