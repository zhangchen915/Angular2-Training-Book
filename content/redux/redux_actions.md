# Redux Actions

Redux操作通常应该是简单的JSON对象。 这是因为它们应该是可序列化和可重播到应用程序状态。 即使你的操作涉及异步逻辑，最终分派的操作仍应为纯JSON对象。

Redux动作创建者通常会发生副作用，例如进行API调用或生成ID。 这是因为当最终动作被分派给reducer时，我们想更新应用程序状态以反映已经发生的情况。

让我们来看看在这个例子中使用的动作。 现在，让我们只关注一些简单的同步操作。

## 同步 Actions

大多数Redux应用程序都有一组称为“动作创建者”的函数，用于设置和分派动作。

在Angular 2中，为您的动作创建者定义“动作创建者服务”是很方便的; 这些服务可以注入到需要分派动作的组件中。

*app/actions/counter-actions.ts*

```typescript
import { Injectable } from '@angular/core';
import { NgRedux } from 'ng2-redux';

export const INCREMENT_COUNTER = 'INCREMENT_COUNTER';
export const DECREMENT_COUNTER = 'DECREMENT_COUNTER';

@Injectable()
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

如你所见，动作创建者是（可选）参数的简单函数，然后分派包含更多信息的JSON对象。

`dispatch` 函数需要使用符合Redux库中“Action”接口的调用：

```
import { Action } from 'redux';

```

此接口具有以下属性：

- *type* - 表示操作的字符串/枚举
- *payload?* - 可选 - 要传递到reducer的数据（如果适用）
- *error?*  - 可选 - 指示此消息是否由于错误
- *metaData?* - 可选 - 任何额外信息

## 异步 Actions

如果你必须处理异步或条件动作，这个“ActionCreatorService”模式就派上用场了（react-redux的用户可能认为这个模式类似于依赖注入世界中的redux-thunk）。

*app/actions/counter-actions.ts*

```typescript
import { Injectable } from '@angular/core';
import { NgRedux } from 'ng2-redux';

export const INCREMENT_COUNTER = 'INCREMENT_COUNTER';
export const DECREMENT_COUNTER = 'DECREMENT_COUNTER';

@Injectable()
export class CounterActions {
  constructor(private redux: NgRedux<any>) {}

  // ...

  incrementIfOdd() {
    const { counter } = this.redux.getState();

    if (counter % 2 === 0) return;
    this.redux.dispatch({ type: INCREMENT_COUNTER });
  }

  incrementAsync(timeInMs = 1000) {
    this.delay(timeInMs).then(() => this.redux.dispatch({ type: INCREMENT_COUNTER }));
  }

  private delay(timeInMs) {
    return new Promise((resolve, reject) => {
      setTimeout(() => resolve() , timeInMs);
    });
  }
}
```

在`incrementIfOdd`操作中，我们使用`getState`函数来获取应用程序的当前状态。

在`incrementAsync`操作中，我们将实际调用延迟到`dispatch`。 例如，我们创建了一个将在延迟后解决的Promise。 一旦Promise解析，我们就可以使用增加动作进行调度。

[View Ng2-Redux Example](https://plnkr.co/edit/wDVKsW7ux3dvxNdD7PO0?p=preview) [View Ngrx Example](https://plnkr.co/edit/7XmqzIwuynA5UGANPft0?p=preview)

## 依赖其它服务的 Actions

ActionCreatorService模式在您的操作创建者必须使用其他Angular 2服务的情况下变得必要。 考虑以下处理远程API调用的ActionCreatorService：

```typescript
import { Injectable } from '@angular/core';
import { NgRedux } from 'ng2-redux';
import { AuthService } from '../services/auth/';

@Injectable()
export class SessionActions {
  static LOGIN_USER_PENDING = 'LOGIN_USER_PENDING';
  static LOGIN_USER_SUCCESS = 'LOGIN_USER_SUCCESS';
  static LOGIN_USER_ERROR = 'LOGIN_USER_ERROR';
  static LOGOUT_USER = 'LOGOUT_USER';

  constructor(
    private ngRedux: NgRedux<any>,
    private authService: AuthService) {}

  loginUser(credentials) {
    const username = credentials.username;
    const password = credentials.password;

    this.ngRedux.dispatch({ type: SessionActions.LOGIN_USER_PENDING });

    this.authService.login(username, password)
      .then(result => this.ngRedux.dispatch({
          type: SessionActions.LOGIN_USER_SUCCESS,
          payload: result
      }))
      .catch(() => this.ngRedux.dispatch({
        type: SessionActions.LOGIN_USER_ERROR
      }));
  };

  logoutUser = () => {
    this.ngRedux.dispatch({ type: SessionActions.LOGOUT_USER });
  };
}
```