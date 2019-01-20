#组件的应用程序结构

概念化Angular应用程序设计的一个有用方法是将它看作一个嵌套组件的树，每个组件都有一个独立的作用域。

 例如，请考虑以下内容：

```html
<rio-todo-app>
  <rio-todo-list>
    <rio-todo-item></rio-todo-item>
    <rio-todo-item></rio-todo-item>
    <rio-todo-item></rio-todo-item>
  </rio-todo-list>
  <rio-todo-form></rio-todo-form>
</rio-todo-app>
```
根节点`rio-todo-app`，包括一个`rio-todo-list`和一个`rio-todo-form`。 在列表中，我们有几个`rio-todo-item`。 这些组件中的每一个对于用户是可见的，他们可以与这些组件交互并且执行动作。