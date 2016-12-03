#使用其他组件

组件依赖于其他组件，指令和管道。例如，TodoList依赖TodoItem。要让组件知道这些依赖关系，我们将它们分组到一个模块中。
```ts
import {NgModule} from '@angular/core';
import {TodoInput} from './components/todo-input';
import {TodoItem} from './components/todo-item';
import {TodoList} from './components/todo-list';

@NgModule({
  imports: [ ... ],
  declarations: [ TodoList, TodoItem, TodoInput ],
  bootstrap: [ ... ]
})
export class ToDoAppModule { }
```
该属性期望作为模块的一部分的组件，指令和管道的数组。
有关NgModule的更多信息，请参阅模块部分。