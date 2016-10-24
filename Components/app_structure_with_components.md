#组件的应用程序结构
 
 一个有用的概念化Angular应用程序设计的方法是将它看成一个嵌套组件的树，每个组件都有一个独立的范围。
 例如，请考虑以下内容：
 ```html
 <TodoApp>
   <TodoList>
     <TodoItem></TodoItem>
     <TodoItem></TodoItem>
     <TodoItem></TodoItem>
   </TodoList>
   <TodoForm></TodoForm>
 </TodoApp>
 ```
根节点`TodoApp`由一个`TodoList`与`TodoForm`组成。在列表中我们有几个`TodoItem`。这些组件对于用户都是可见的，用户可以与这些组件进行交互。