# 覆盖测试的依赖关系

`TestBed`提供了几个函数，允许我们覆盖测试模块中使用的依赖项。

- `overrideModule`
- `overrideComponent`
- `overrideDirective`
- `overridePipe`

例如，您可能想要覆盖组件的模板。这对于测试大型组件的一小部分很有用，因为您可以忽略DOM的其余部分的输出，并且只关注您感兴趣的部分。

```typescript
import {Component} from '@angular/core';

@Component({
  selector: 'display-message',
  template: `
    <div>
      <div>
        <h1>{{message}}</h1>
      <div>
    </div>
  `
})
export class MessageComponent {
  public message: string = '';

  setMessage(newMessage: string) {
      this.message = newMessage;
  }
}
```
```typescript
import {MessageComponent} from './message.component';
import { provide } from '@angular/core';
import {
  async,
  inject,
  TestBed,
} from '@angular/core/testing';

describe('MessageComponent', () => {

  let fixture;

  beforeEach(() => {
    TestBed.configureTestingModule({
      declarations: [MessageComponent],
      providers: []
    });

    fixture = TestBed.overrideComponent(MessageComponent, {
      set: {
        template: '<span>{{message}}</span>'
      }})
      .createComponent(MessageComponent);

    fixture.detectChanges();
  });

  it('should set the message', async(inject([], () => {
    fixture.componentInstance.setMessage('Test message');
    fixture.detectChanges();
    fixture.whenStable().then(() => {
      const compiled = fixture.debugElement.nativeElement;
      expect(compiled.querySelector('span').innerText).toEqual('Test message');
    });
  })));

});
```

[View Example](http://plnkr.co/edit/P4tkaUYBFcHGvoTZjKnB?p=preview)