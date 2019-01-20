# 测试异步操作

有时候，我们需要测试依赖于在特定时间发生的异步操作的组件。 Angular提供了一个称为`fakeAsync`的函数，它将我们的测试包裹在一个区域中，并让我们访问`tick`函数，这将允许我们精确地模拟时间的流逝。

让我们回到`QuoteComponent`组件的例子，并使用`fakeAsync`重写单元测试：

```typescript
import { Component } from '@angular/core';
import { QuoteService } from './quote.service';

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

```typescript
import { QuoteService } from './quote.service';
import { QuoteComponent } from './quote.component';
import { provide } from '@angular/core';
import {
  async,
  TestBed,
  fakeAsync,
  tick,
} from '@angular/core/testing';

class MockQuoteService {
  public quote: string = 'Test quote';

  getQuote() {
    return Promise.resolve(this.quote);
  }
}

describe('Testing Quote Component', () => {

  let fixture;

  beforeEach(() => {
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

  it('Should get quote', fakeAsync(() => {
    fixture.componentInstance.getQuote();
    tick();
    fixture.detectChanges();
    const compiled = fixture.debugElement.nativeElement;
    expect(compiled.querySelector('div').innerText).toEqual('Test quote');
  }));
});
```

[View Example](http://plnkr.co/edit/W7zHfjFvEGYW0BBNdQlU?p=preview)

这里我们有一个`QuoteComponent，`它有一个`getQuote`触发异步更新。 我们将整个测试包装在`fakeAsync`中，这将允许我们通过调用`tick()`来使用同步函数调用来测试我们的组件(`getQuote()`)的异步行为。 然后我们可以运行`detectChanges`并查询DOM的预期结果。