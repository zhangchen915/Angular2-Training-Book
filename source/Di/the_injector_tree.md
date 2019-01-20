# 注入树

Angular 2注入器（一般）返回单例。 也就是说，在前面的示例中，应用程序中的所有组件都将接收相同的随机数。 在Angular1.x中只有一个注入器，并且所有服务都是单例。 Angular 2通过使用注入器树来克服这个限制。

在Angular 2中，每个应用程序不只有一个注入器，每个应用程序至少有一个注入器。 注入器被组织在与Angular 2的组件树平行的树中。

考虑下面的树，它是一个包含两个打开的聊天窗口和登录/注销小部件的聊天应用程序的模型。


![Image of a Component Tree, and a DI Tree](../assets/di-tree.png)Figure: Image of a Component Tree, and a DI Tree


在上图中，有一个根注入器，它通过`@NgModule`的`providers`数组建立。有一个`LoginService`注册到根注入器。

根注入器下面是根`@Component`。这个特定的组件没有 `providers` 数组，并将使用根注入器的所有依赖项。

还有两个子注入器，每个`ChatWindow`组件一个。每个组件都有自己的`ChatService`实例。

第三个子组件是`Logout/Login`，但它没有注入器。

有几个没有注射器的孙子组件。每个`ChatWindow`有`ChatFeed`和`ChatInput`组件。还有`LoginWidget`和`LogoutWidget`组件，其中`Logout/Login`作为它们的父组件。

注入器树不会为每个组件创建新的注入器，但会为每个在其装饰器中具有`providers`数组的组件创建一个新的注入器。

没有`providers` 数组的组件查看其注册器的父组件。如果父级没有注入器，它将查找，直到它到达根注入器。

*警告：*请小心使用`providers` 数组。如果子组件使用父组件的`providers`数组中依赖进行装饰，则子组件将影响父组件的依赖关系。这可能带来各种意想不到的后果。

考虑下面的例子：

app/module.ts

```typescript
import { NgModule } from '@angular/core';
import { BrowserModule } from '@angular/platform-browser';
import { App } from './app.component';
import { ChildInheritor, ChildOwnInjector } from './components/index';
import { Unique } from './services/unique';


const randomFactory = () => { return Math.random(); };

@NgModule({
  imports: [
    BrowserModule
  ],
  declarations: [
    App,
    ChildInheritor,
    ChildOwnInjector,
  ],
  /** Provide dependencies here */
  providers: [
    Unique,
  ],
  bootstrap: [ App ],
})
export class AppModule {}
```

在上面的示例中，`Unique`被引导到根注入器。

*app/services/unique.ts*

```typescript
import { Injectable } from '@angular/core';

@Injectable()
export class Unique {
  value: string;
  constructor() {
    this.value = (+Date.now()).toString(16) + '.' +
      Math.floor(Math.random() * 500);
  }
}
```

`Unique`服务在实例化时生成其实例唯一的值。

*app/components/child-inheritor.component.ts*

```typescript
import { Component, Inject } from '@angular/core';
import { Unique } from '../services/unique';

@Component({
  selector: 'child-inheritor',
  template: `<span>{{ value }}</span>`
})
export class ChildInheritor {
  value: number;
  constructor(u: Unique) {
    this.value = u.value;
  }
}
```

子继承器没有注入器。它将向上遍历组件树，寻找注入器。

*app/components/child-own-injector.component.ts*

```typescript
import { Component, Inject } from '@angular/core';
import { Unique } from '../services/unique';

@Component({
  selector: 'child-own-injector',
  template: `<span>{{ value }}</span>`,
  providers: [Unique]
})
export class ChildOwnInjector {
  value: number;
  constructor(u: Unique) {
    this.value = u.value;
  }
}
```

The child own injector component has an injector that is populated with its own instance of `Unique`. This component will not share the same value as the root injector's `Unique` instance.

子组件自己的注入组件有一个注入器，它填充了自己的`Unique`实例。 此组件不会与根注入器的`Unique`实例共享相同的值。

*app/containers/app.ts*

```typescript
import { Component, Inject } from '@angular/core';
import { Unique } from '../services/unique';

@Component({
  selector: 'app',
  template: `
     <p>
     App's Unique dependency has a value of {{ value }}
     </p>
     <p>
     which should match
     </p>
     <p>
     ChildInheritor's value: <child-inheritor></child-inheritor>
     </p>
     <p>
     However,
     </p>
     <p>
     ChildOwnInjector should have its own value: <child-own-injector></child-own-injector>
     <p>
     ChildOwnInjector's other instance should also have its own value <child-own-injector></child-own-injector>
     </p>
       `,
})
export class App {
  value: number;
  constructor(u: Unique) {
    this.value = u.value;
  }
}
```

[View Example](http://plnkr.co/edit/5jHB4TP3IpnkWJq2wzeX?p=preview)