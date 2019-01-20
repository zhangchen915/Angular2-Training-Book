# 测试简单的 Action

考虑下面的简单动作，取自本书的Redux章节：

```typescript
import { Injectable } from '@angular/core';
import { NgRedux } from 'ng2-redux';

export const INCREMENT_COUNTER = 'INCREMENT_COUNTER';
export const DECREMENT_COUNTER = 'DECREMENT_COUNTER';

@Injectable
export class CounterActions {
  constructor(private redux: NgRedux<any>) {}

  increment() {
    this.redux.dispatch({ type: INCREMENT_COUNTER });
  }

  decrement() {
    this.redux.dispatch({ type: DECREMENT_COUNTER });
  }
}
```

这些是很简单的测试：

```typescript
import { NgRedux } from 'ng2-redux';
import {
  CounterActions,
  INCREMENT_COUNTER,
  DECREMENT_COUNTER,
} from './counter';

// Mock out the NgRedux class with just enough to test what we want.
class MockRedux extends NgRedux<any> {
  constructor() {
    super(null);
  }
  dispatch = () => undefined;
}

describe('counter action creators', () => {
  let actions: CounterActions;
  let mockRedux: NgRedux<any>;

  beforeEach(() => {
    // Initialize mock NgRedux and create a new instance of the
    // ActionCreatorService to be tested.
    mockRedux = new MockRedux();
    actions = new CounterActions(mockRedux);
  });

  it('increment should dispatch INCREMENT_COUNTER action', () => {
    const expectedAction = {
      type: INCREMENT_COUNTER
    };

    spyOn(mockRedux, 'dispatch');
    actions.increment();

    expect(mockRedux.dispatch).toHaveBeenCalled();
    expect(mockRedux.dispatch).toHaveBeenCalledWith(expectedAction);
  });

  it('decrement should dispatch DECREMENT_COUNTER action', () => {
    const expectedAction = {
      type: DECREMENT_COUNTER
    };

    spyOn(mockRedux, 'dispatch');
    actions.decrement();

    expect(mockRedux.dispatch).toHaveBeenCalled();
    expect(mockRedux.dispatch).toHaveBeenCalledWith(expectedAction);
  });
});
```

我们只是确保我们的action创建者正确地分派actions。