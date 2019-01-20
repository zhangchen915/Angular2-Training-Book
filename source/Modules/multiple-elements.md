# 将组件，管道和服务添加到模块

在上一节中，我们学习了如何使用一个组件创建一个模块，但通常模块不可能只有一个组件。 我们的模块通常由多个组件，服务，指令和管道组成。 在本章中，我们将扩展我们之前使用自定义组件，管道和服务的示例。

让我们开始定义一个新的组件，我们将用来显示信用卡信息。

*credit-card.component.ts*

```typescript
import { Component, OnInit } from '@angular/core';
import { CreditCardService } from './credit-card.service';

@Component({
  selector: 'app-credit-card',
  template: `
    <p>Your credit card is: {{ creditCardNumber | creditCardMask }}</p>
  `
})
export class CreditCardComponent implements OnInit {
  creditCardNumber: string;

  constructor(private creditCardService: CreditCardService) {}

  ngOnInit() {
    this.creditCardNumber = this.creditCardService.getCreditCard();
  }
}
```

此组件依靠`CreditCardService`获取信用卡号，并通过`creditCardMask`管道隐藏掉除最后4位之外的数字。

*credit-card.service.ts*

```typescript
import { Injectable } from '@angular/core';

@Injectable()
export class CreditCardService {
  getCreditCard(): string {
    return '2131313133123174098';
  }
}
```

*credit-card-mask.pipe.ts*

```typescript
import { Pipe, PipeTransform } from '@angular/core';

@Pipe({
  name: 'creditCardMask'
})
export class CreditCardMaskPipe implements PipeTransform {
  transform(plainCreditCard: string): string {
    const visibleDigits = 4;
    let maskedSection = plainCreditCard.slice(0, -visibleDigits);
    let visibleSection = plainCreditCard.slice(-visibleDigits);
    return maskedSection.replace(/./g, '*') + visibleSection;
  }
}
```

一切就绪后，我们现在可以在根组件中使用`CreditCardComponent` 。

*app.component.ts*

```typescript
mport { Component } from "@angular/core";

@Component({
  selector: 'app-root',
  template: `
    <h1>My Angular 2 App</h1>
    <app-credit-card></app-credit-card>
  `
})
export class AppComponent {}
```

当然，为了能够使用这个新的组件，管道和服务，我们需要更新我们的模块，否则Angular不能够编译我们的应用程序。

*app.module.ts*

```typescript
import { NgModule } from '@angular/core';
import { BrowserModule } from '@angular/platform-browser';

import { AppComponent } from './app.component';

import { CreditCardMaskPipe } from './credit-card-mask.pipe';
import { CreditCardService } from './credit-card.service';
import { CreditCardComponent } from './credit-card.component';

@NgModule({
  imports: [BrowserModule],
  providers: [CreditCardService],
  declarations: [
    AppComponent,
    CreditCardMaskPipe,
    CreditCardComponent
  ],
  bootstrap: [AppComponent]
})
export class AppModule { }
```

注意，我们已将组件`CreditCardComponent`和管道`CreditCardMaskPipe`与模块`AppComponent`的根组件一起添加到声明属性中。 另一方面，我们的自定义服务使用具有`providers`属性的依赖注入系统进行配置。

[View Example](https://plnkr.co/edit/Jab7Wsijc9w0rbgdIBDO?p=preview)

请注意，在`providers`属性中定义服务的此方法**只应在根模块中使用**。 在功能模块中执行此操作将在使用延迟加载模块时导致意外的后果。

在下一节中，我们将了解如何安全地定义功能模块中的服务。