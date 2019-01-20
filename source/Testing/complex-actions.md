# 测试复杂的 Actions

当我们想测试异步或条件Actions创建者时，事情变得有点棘手。我们的目标仍然是相同的：确保 operations 发出我们期望的Actions。

## 条件 Action

考虑以下条件action（即，根据当前状态触发的操作）：

```typescript
import { Injectable } from '@angular/core';
import { NgRedux } from 'ng2-redux';
export const INCREMENT_COUNTER = 'INCREMENT_COUNTER';

@Injectable()
export class MyActionService {
  constructor(private redux: NgRedux) {};

  // A conditional action
  incrementIfOdd() {
    const { counter } = this.redux.getState();

    if (counter % 2 === 0) return;
    this.redux.dispatch({ type: INCREMENT_COUNTER });
  }
}
```

单元测试与之前类似，但我们需要模拟我们的状态以及dispatch：

```typescript
import { NgRedux } from 'ng2-redux';
import { CounterActions } from './counter';

class MockRedux extends NgRedux<any> {
  constructor(private state: any) {
    super(null);
  }
  dispatch = () => undefined;
  getState = () => this.state;
}

describe('counter action creators', () => {
  let actions: CounterActions;
  let mockRedux: NgRedux<any>;
  let mockState: any = {};

  beforeEach(() => {
    // Our mock NgRedux can now accept mock state as a constructor param.
    mockRedux = new MockRedux(mockState);
    actions = new CounterActions(mockRedux);
  });

  it('incrementIfOdd should dispatch INCREMENT_COUNTER action if count is odd',
    () => {
      // Our tests can bake in the initial state they need.
      const expectedAction = {
        type: CounterActions.INCREMENT_COUNTER
      };

      mockState.counter = 3;
      spyOn(mockRedux, 'dispatch');
      actions.incrementIfOdd();

      expect(mockRedux.dispatch).toHaveBeenCalled();
      expect(mockRedux.dispatch).toHaveBeenCalledWith(expectedAction);
    });

  it('incrementIfOdd should not dispatch INCREMENT_COUNTER action if count is even',
    () => {
      mockState.counter = 2;
      spyOn(mockRedux, 'dispatch');
      actions.incrementIfOdd();

      expect(mockRedux.dispatch).not.toHaveBeenCalled();
    });
});
```

## 异步 Action

下面异步Action怎么办？

```typescript
import { Injectable } from '@angular/core';
import { NgRedux } from 'ng2-redux';

export const INCREMENT_COUNTER = 'INCREMENT_COUNTER';
export const DECREMENT_COUNTER = 'DECREMENT_COUNTER';

@Injectable()
export class CounterActions {
  constructor(private redux: NgRedux<any>) {}

  // ...

  incrementAsync(timeInMs = 1000) {
    this.delay(timeInMs).then(() => this.redux.dispatch({ type: INCREMENT_COUNTER }));
  }

  private delay(timeInMs) {
    return new Promise((resolve,reject) => {
      setTimeout(() => resolve() , timeInMs);
    });
  }
}
```

我们可以使用异步服务函数的常规技术来测试：

- 如果我们可以让`incrementAsync`返回一个promise，我们可以从测试中返回一个promise，并且`jasmine`将一直等到它完成。
- 或者，我们可以使用在测试组件一节中讨论的`fakeAsync`技术。

要记住的是，如果我们遵循`ActionCreatorService`模式，我们的操作只是Angular服务上的函数。 所以我们可以模拟出NgRedux（和任何其他依赖项），就像测试其他Angular 2服务一样。