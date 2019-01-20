#使用其他组件

组件依赖于其他组件，指令和管道。例如，`TodoListComponent`依赖于`TodoItemComponent` 。要让组件知道这些依赖关系，我们将它们分组到一个模块中。

```typescript
import {NgModule} from '@angular/core';

import {TodoListComponent} from './components/todo-list.component';
import {TodoItemComponent} from './components/todo-item.component';
import {TodoInputComponent} from './components/todo-input.component';

@NgModule({
  imports: [ ... ],
  declarations: [
    TodoListComponent,
    TodoItemComponent,
    TodoInputComponent
  ],
  bootstrap: [ ... ]
})
export class ToDoAppModule {
}
```

`declarations`属性期望作为模块的一部分的组件，指令和管道的数组。

有关`NgModule`的更多信息，请参阅 [模块部分](https://angular-2-training-book.rangle.io/handout/modules/) 。