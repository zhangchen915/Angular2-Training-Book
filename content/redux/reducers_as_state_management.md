# Reducers 的 State 管理

这个简单的想法证明是非常强大的。 使用Redux，你可以将一系列事件重播到reducer中，从而获得新的应用程序状态。

Redux应用程序中的Reducer不应该改变state，而是返回一个副本，并且无副作用。 这鼓励你把你的应用程序想象成从一系列事件中“计算”的UI。

让我们来看看一个简单的计数器reducer。

## 简单的 Reducer

*app/reducer/counter-reducer.ts*

```typescript
import { INCREMENT_COUNTER, DECREMENT_COUNTER } from '../actions/counter-actions';

export default function counter(state = 0, action) {
  switch (action.type) {
    case INCREMENT_COUNTER:
      return state + 1;
    case DECREMENT_COUNTER:
      return state - 1;
    default:
      return state;
  }
}
```

我们可以看到，我们正在传递一个初始状态和一个动作。为了处理每个动作，我们设置了一个switch语句。而不是每个reducer需要显式订阅分发器，每个动作都会传递到每个reducer，它处理它感兴趣的动作，然后返回新状态到下一个reducer。

Reducers 应无副作用。这意味着他们不应该修改自己范围之外的东西。他们应该简单地计算下一个应用程序状态作为reducer参数的纯函数。

出于这个原因，诸如更新数据库中的记录，生成id等的副作用引起操作应该在应用中的其他地方处理，例如在动作创建者中，使用中间件例如  ['Epics' from redux-observable](https://github.com/redux-observable/redux-observable) 或 [ngrx/effects](https://github.com/ngrx/effects)。

创建reducer时的另一个考虑因素是确保它们是不可变的，而不是修改应用程序的状态。如果您改变应用程序状态，它可能会导致意外的行为。有几种方法可以帮助维护reducer的不变性。一种方法是使用ES6的新功能，如`Object.assign`或数组的展开运算符。

```typescript
function immutableObjectReducer(state = { someValue: 'value'} , action) {
  switch(action.payload) {
    case SOME_ACTION:
      return Object.assign({}, state, { someValue: action.payload.value });
    default:
      return state;
  }
}

function immutableArrayReducer(state = [1,2,3], action) {
  switch(action.payload) {
    case ADD_ITEM:
      return [...state,action.payload.value]
    default:
      return state;
  }
}
```

但是，当处理复杂或深层嵌套的对象时，可能很难使用此语法在应用程序中维护不变性。这种情况下像Immutable.js这样的库可以提供帮助。