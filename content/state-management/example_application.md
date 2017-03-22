# 示例应用

在本章中，您将使用@ngrx创建一个简单的计数器应用程序。 您的应用程序将允许用户将数字递增和递减1，并将该值重置为零。 这是我们将在整个示例中使用的AppState：

*app/models/appState.ts*

```typescript
import {Counter} from './counter';

export interface AppState {
  readonly counter: Counter;
}
```

*app/models/counter.ts*

```typescript
export interface Counter {
  readonly currentValue: number;
}
```

> 在自己的文件中声明每个接口是很好的做法，如果您的应用程序使用了七个或更多接口，则创建一个逻辑目录结构。

