# 通过Dispatching Actions 修改应用的 State

大多数Redux应用程序都有一组功能，称为“action creators”，用于设置和dispatch action。

在Angular中，将您的操作创建者定义为`@Injectable()`服务非常方便，将dispatch，创建和副作用逻辑与应用程序中的`@Component`类分离。

## 同步Actions

*app/store/counter/counter.actions.ts*

```typescript
import {Injectable} from '@angular/core';
import {Store} from '@ngrx/store';

import {createAction} from '../createAction';
import {AppState} from '../../models/appState';

@Injectable()
export class CounterActions {

  static INCREMENT = 'INCREMENT';
  static DECREMENT = 'DECREMENT';
  static RESET = 'RESET';

  constructor(private store: Store<AppState>) {

  }

  increment() {
    this.store.dispatch(createAction(CounterActions.INCREMENT));
  }

  decrement() {
    this.store.dispatch(createAction(CounterActions.DECREMENT));
  }

  reset() {
    this.store.dispatch(createAction(CounterActions.RESET));
  }

}
```

如你所见，动作创建者是简单的函数，它dispatch包含更多描述状态修改信息的Action对象。

## 异步 Actions

如果必须处理异步或条件actions（react-redux 用户可能将这种模式识别为依赖注入世界中的redux-thunk），则“ActionCreatorService”模式非常方便。

*app/store/counter/counter.actions.ts*

```typescript
import {Injectable} from '@angular/core';
import {Store} from '@ngrx/store';

import {createAction} from '../createAction';
import {AppState} from '../../models/appState';


@Injectable()
export class CounterActions {

  constructor(private store: Store<AppState>) {

  }

  incrementIfOdd() {
    this.store.select(appState => appState.counter.currentValue)
      .take(1)
      .subscribe(currentValue => {
        if (currentValue % 2 !== 0) {
          this.store.dispatch(createAction(CounterActions.INCREMENT);
        }
      });
  }

  incrementAsync(timeInMs: number = 1000) {
    this.delay(timeInMs).then(() => this.store.dispatch(createAction(CounterActions.INCREMENT)));
  }

  private delay(timeInMs: number) {
    return new Promise((resolve) => {
      setTimeout(() => resolve() , timeInMs);
    });
  }

}
```

在`incrementIfOdd()`动作创建者中，我们在应用程序 state 中创建一个计时器的`currentValue`的一次性订阅。在那里，我们在dispatching action之前是检查是否为奇数。

在`incrementAsync()`动作创建者中，我们延迟对`dispatch()`的实际调用。我们创造了一个在延迟后解析的`Promise`。一旦`Promise`解析了，我们可以dispatch一个action来增加计数器。

## 依赖其它服务的 Actions

在你的操作创建者必须使用其他角色服务的情况下，ActionCreatorService模式才是必需的。考虑下面的SessionActions服务来处理远程API调用：

```typescript
import {Injectable} from '@angular/core';
import {Store} from '@ngrx/store';

import {createAction} from '../createAction';
import {AppState} from '../../models/appState';

@Injectable()
export class SessionActions {

  static LOGIN_USER_PENDING = 'LOGIN_USER_PENDING';
  static LOGIN_USER_SUCCESS = 'LOGIN_USER_SUCCESS';
  static LOGIN_USER_ERROR = 'LOGIN_USER_ERROR';
  static LOGOUT_USER = 'LOGOUT_USER';

  constructor(
    private store: Store<AppState>,
    private authService: AuthService
  ) {

  }

  loginUser(credentials: any) {
    this.store.dispatch(createAction(SessionActions.LOGIN_USER_PENDING));

    this.authService.login(credentials.username, credentials.password)
      .then(result => this.store.dispatch(createAction(SessionActions.LOGIN_USER_SUCCESS, result)))
      .catch(() => this.store.dispatch(createAction(SessionActions.LOGIN_USER_ERROR)));
  };

  logoutUser() {
    this.store.dispatch(createAction(SessionActions.LOGOUT_USER));
  };

}
```