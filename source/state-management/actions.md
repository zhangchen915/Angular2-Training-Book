# Actions

Redux使用一个称为Actions的概念，它描述了对应用程序的状态更改。 Redux actions 是实现@ngrx提供的`Action`接口的简单JSON对象：

```typescript
export interface Action {
  type: string;
  payload?: any;
}
```

`type`属性是用于标识您的应用程序的操作的唯一字符串。 使用*lisp-case*（例如`MY_ACTION`）是一个常见的惯例，但是只要在整个项目中是一致的，您可以随意使用任何写法。

`payload` 属性提供了一种将附加数据传递到Redux的其他部分的方法，它是完全可选的。

示例：

```typescript
const loginSendAction: Action = {
  type: 'LOGIN_SEND',
  payload: {
    username: 'katie',
    password: '35c0cd1ecbbb68c75498b83c4e79fe2b'
  }
};
```

> 使用平面对象，使得动作是可序列化的，并且可以重播到应用程序状态。 即使您的操作涉及异步逻辑，最终的分派操作仍将是一个简单的JSON对象。

为了简化操作创建，您可以创建一个工厂函数来处理应用程序中重复的部分：

*app/store/createAction.ts*

```typescript
import {Action} from '@ngrx/store';

export function createAction(type, payload?): Action {
  return { type, payload };
}
```

由此产生的`LOGIN_SEND`创建操作变得更加简洁和干净：

```typescript
const loginSendAction: Action = createAction('LOGIN_SEND', {
  username: 'katie',
  password: '35c0cd1ecbbb68c75498b83c4e79fe2b'
});
```