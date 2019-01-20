# 验证方法和属性

我们可以相当容易地测试简单Angular 2组件的属性和方法 - 毕竟，Angular 2组件是我们可以创建和接口的简单类。 说我们有一个简单的组件，保持显示一个定义的消息。 消息的内容可以通过`setMessage`函数改变，而`clearMessage`函数将会设置一个空消息。 这是一个非常小的组件，但我们将如何测试呢？

*message.component.ts*

```typescript
import {Component} from '@angular/core';

@Component({
  selector: 'display-message',
  template: '<h1>{{message}}</h1>'
})

export class MessageComponent {
  public message: string = '';

  constructor() {}

  setMessage(newMessage: string) {
      this.message = newMessage;
  }

  clearMessage() {
    this.message = '';
  }
}
```

现在我们将创建两个单元测试，一个测试`setMessage`函数以查看是否显示新消息，另一个测试`clearMessage`函数以查看清除消息是否按预期工作。

*message.spec.ts*

```typescript
import {MessageComponent} from './message.component';

describe('Testing message state in message.component', () => {
  let app: MessageComponent;

  beforeEach(() => {
    app = new MessageComponent();
  });

  it('should set new message', () => {
    app.setMessage('Testing');
    expect(app.message).toBe('Testing');
  });

  it('should clear message', () => {
    app.clearMessage();
    expect(app.message).toBe('');
  });
});
```

[View Example](http://plnkr.co/edit/XUM8Gfz08nfbQf1BhDN1?p=preview)

我们创建了两个测试：一个用于`setMessage，`另一个用于`clearMessage`。 为了调用这些函数，我们必须首先初始化`MessageComponent`类。 这是通过在执行每个测试之前调用`beforeEach`函数来实现的。
一旦我们的`MessageComponent`对象被创建，我们可以调用`setMessage`和`clearMessage`并分析这些动作的结果。 我们制定一个预期的结果，然后测试看看我们期望的结果是否是。 这里我们测试我们试图设置的消息是否将`MessageComponent`属性消息修改为我们想要的值。 如果是，那么测试成功，我们的`MessageComponent`按预期工作。