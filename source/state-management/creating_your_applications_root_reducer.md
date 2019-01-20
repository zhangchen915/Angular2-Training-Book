# 创建应用的Root Reducer

@ngrx允许我们将您的应用程序分解成具有单个关注领域的较小的reducer。 我们可以通过创建一个反映应用程序`AppState`的对象来组合这些reducer，其中每个属性将指向其中一个较小的reducer。

*app/store/rootReducer.ts*

```typescript
import {counterReducer} from './counter/counter.reducer';

export const rootReducer = {
  counter: counterReducer
};
```