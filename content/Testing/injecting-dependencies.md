# 注入依赖和DOM修改

在前面的例子中，我们测试的类`MessageComponent`没有任何注入的依赖。 在Angular 2中，组件通常依赖于服务和其他类（管道/提供程序等）来运行，这将被注入到组件类的构造函数中。 当测试这些组件时，我们自己注入依赖。 由于这是一个Angular特定的例程，没有用于完成这个的纯Jasmine函数。 Angular在`@angular/core/testing`中提供了许多函数，使我们能够有效地测试我们的组件。 让我们来看一个基本组件：

*quote.component.ts*

```typescript
import { QuoteService } from './quote.service';
import { Component } from '@angular/core';

@Component({
  selector: 'my-quote',
  template: '<h3>Random Quote</h3> <div>{{quote}}</div>'
})

export class QuoteComponent {
  quote: string;

  constructor(private quoteService: QuoteService){};

  getQuote() {
    this.quoteService.getQuote().then((quote) => {
      this.quote = quote;
    });
  };
}
```

此组件依赖于`QuoteService`来获取随机报价，然后它将显示。 类很简单 - 它只有`getQuote`函数，它将修改DOM，因此它将是我们测试的主要领域。
为了测试这个组件，我们需要启动`QuoteComponent`类。 Angular测试库提供了一个名为`TestBed`的实用程序。 这允许我们配置一个测试模块，我们可以提供模拟依赖。 此外，它将为我们创建组件并返回一个组件夹具，我们可以对其执行测试操作。

*quote.spec.ts*

```typescript
import { QuoteService } from './quote.service';
import { QuoteComponent } from './quote.component';
import { provide, destroyPlatform } from '@angular/core';
import {
  async,
  inject,
  TestBed,
} from '@angular/core/testing';
import {
  BrowserDynamicTestingModule,
  platformBrowserDynamicTesting
} from '@angular/platform-browser-dynamic/testing';

class MockQuoteService {
  public quote: string = 'Test quote';

  getQuote() {
    return Promise.resolve(this.quote);
  }
}

describe('Testing Quote Component', () => {

  let fixture;

  beforeEach(() => destroyPlatform());

  beforeEach(() => {
    TestBed.initTestEnvironment(
      BrowserDynamicTestingModule,
      platformBrowserDynamicTesting()
    );

    TestBed.configureTestingModule({
      declarations: [
        QuoteComponent
      ],
      providers: [
        { provide: QuoteService, useClass: MockQuoteService }
      ]
    });
    fixture = TestBed.createComponent(QuoteComponent);
    fixture.detectChanges();
  });

  it('Should get quote', async(inject([], () => {
    fixture.componentInstance.getQuote();
    fixture.whenStable()
      .then(() => {
        fixture.detectChanges();
        return fixture.whenStable();
      })
      .then(() => {
        const compiled = fixture.debugElement.nativeElement;
        expect(compiled.querySelector('div').innerText).toEqual('Test quote');
      });
  })));
});
```

[View Example](http://plnkr.co/edit/7KZu1Yg6kBX7rksrpRHV?p=preview)

测试QuoteComponent是一个相当简单的过程。我们想创建一个QuoteComponent，给它一个报价，看看它是否出现在DOM中。这个过程需要我们创建组件，传入任何依赖项，触发组件执行一个动作，然后查看DOM，看看动作是否是我们所期望的。
让我们来看看上面的单元测试是如何实现的。
我们使用`TestBed.initTestingEnvironment`创建一个使用`BrowserDynamicTestingModule`和`platformBrowserDynamicTesting`作为参数的测试平台，它们也从Angular导入，并允许应用程序被引导进行测试。这对于使用`TestBed`的所有单元测试都是必要的。请注意，在每次测试运行之前，该平台将被销毁并重置。
我们使用`TestBed.configureTestingModule`来提供我们的组件所需的任何依赖项。这里我们的组件依赖于QuoteService来获取数据。我们自己模拟这些数据，从而让我们控制我们期望出现的价值。优秀的做法是将组件测试与服务测试分开 - 这使得测试更容易，因为您一次只关注应用程序的一个方面。如果您的服务或组件失败，您将如何知道哪个是罪魁祸首？我们使用我们的mock类`MockQuoteService`注入QuoteService依赖，在这里我们将为组件提供模拟数据来消费。
接下来我们使用`TestBed.createComponent`（QuoteComponent）创建一个夹具，供我们在我们的测试中使用。这将创建一个我们的组件的新实例，完成任何角度特定的例程，如依赖注入。 `fixture`是一个强大的工具，允许我们查询组件渲染的DOM，以及更改DOM元素和组件属性。它是测试组件的主要接入点，我们广泛使用它。
在应得到报价测试中，我们通过`fixture.componentInstance`属性访问了我们的组件。然后我们调用getQuote来启动我们在QuoteComponent组件中的唯一操作。我们通过使用它的whenStable方法来运行测试，该方法将确保`getQuote()`中的promise已经解决，给组件一个设置报价值的机会。我们调用fixture.detectChanges来监视对DOM发生的任何更改，并使用`fixture.debugElement.nativeElement`属性来访问那些基础DOM元素。
现在我们可以检查由我们的QuoteComponent渲染的DOM是否包含我们通过QuoteService设置的报价。最后一行试图断言DOM的div标签包含模拟报价'Test Quote'。如果是，则我们的组件通过测试并按预期工作;如果没有，这意味着我们的组件不正确地输出引号。
我们的报价测试包在`async()`中。这是为了允许我们的测试在异步测试区域运行。使用异步将创建一个测试区域，这将确保所有异步功能在结束测试之前已经完成。