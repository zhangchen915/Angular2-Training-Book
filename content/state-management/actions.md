# Actions

Redux使用一个称为Actions的概念，它描述了对应用程序的状态更改。 Redux操作是实现@ngrx提供的Action接口的简单JSON对象：

```typescript
export interface Action {
  type: string;
  payload?: any;
}
```

The `type` property is a string used to uniquely identify your action to your application. It's a common convention to use *lisp-case* (such as `MY_ACTION`), however you are free to use whatever casing style that makes to your team, as long as it's consistent across the project.

The `payload` property provides a way to pass additional data to other parts of Redux, and it's entirely optional.

Here is an example:

```typescript
const loginSendAction: Action = {
  type: 'LOGIN_SEND',
  payload: {
    username: 'katie',
    password: '35c0cd1ecbbb68c75498b83c4e79fe2b'
  }
};
```

> Plain objects are used so that the actions are serializable and can be replayable into the application state. Even if your actions involve asynchronous logic, the final dispatched action will remain a plain JSON object.

To simplify action creation, you can create a factory function to take care of the repeating parts within your application:

*app/store/createAction.ts*

```typescript
import {Action} from '@ngrx/store';

export function createAction(type, payload?): Action {
  return { type, payload };
}
```

由此产生的`LOGIN_SEND`操作的创建变得更加简洁和干净：

```typescript
const loginSendAction: Action = createAction('LOGIN_SEND', {
  username: 'katie',
  password: '35c0cd1ecbbb68c75498b83c4e79fe2b'
});
```