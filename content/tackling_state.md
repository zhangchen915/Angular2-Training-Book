# [状态管理](https://vsavkin.com/managing-state-in-angular-2-applications-caf78d123d02#.4v0bjowdr)

作者：Victor Savkin

管理应用程序状态是个难题。您需要在多个后端，Web workers和UI组件之间进行协调。 像Redux和Flux这样的模式旨在通过使这种协调更加明确来解决这个问题。在本文中，我将展示如何使用RxJS在几行代码中实现类似的模式。然后我将展示如何使用这种模式来实现一个简单的Angular 2应用。

## 核心属性

在谈论架构模式时，我喜欢从描述其核心属性开始。你可以写在餐巾背上的东西。The devil, of course, is in the details, and we will get to that. But a high-level overview is useful nevertheless.

在许多方面，我们将要建立的类似于Redux。

#### 不可变 State

**整个应用程序状态被存储为不可变的数据结构。因此，每当发生变化时，都会构建数据结构的新实例。**即使这似乎是初步限制的，但是这个约束导致许多很好的特性。其中之一是它可以使您的Angular应用程序更快一点。

#### Interaction = Action

**更新应用程序状态的唯一方法是发出一个 action。**因此，大多数用户交互都会发出动作。如果你想模拟一个交互，你只需要发出正确的动作序列。

#### Application is fn(a:Observable):Observable

应用程序的逻辑表达为将动作的可观察值映射到可观察到的应用程序状态的函数。

```
function stateFn(
   initState: AppState, 
   actions: Observable<action>): Observable<appstate> { … }
```

此函数仅调用一次。这与Redux不同，Redux在每个动作上都调用了reduce函数。

#### 应用和视图边界

**应用程序和视图逻辑完全分离。**dispatcher 和 state 对象是应用程序和视图通信的边界。该视图使用 dispatcher 发出 actions 并监听 state 中的更改。



### 示例

于这种模式与Redux类似，您可以从观看Dan Abramov的优秀视频课程中受益。在这个课程中，Dan演示如何使用Redux构建一个todo应用。为了更容易比较Redux实现和我的实现，我将在本文中构建相同的应用。

#### 应用的 State

通过查看其 state 的类型定义及其 actions 列表，我可以很好地了解应用程序的功能。所以让我们开始吧。

```
interface Todo { 
  id: number; 
  text: string; 
  completed: boolean; 
} 

interface AppState { 
  todos: Todo[]; 
  visibilityFilter: string; 
}
```

我们的应用程序的状态只是一组todos和一个过滤器，用于定义要显示的todos。

#### Actions

我们的应用程序将支持以下三个 actions：AddTodoAction，ToggleTodoAction和SetVisibilityFilter。

```
class AddTodoAction { 
  constructor(public todoId: number, public text: string){} 
} 

class ToggleTodoAction { 
  constructor(public id: number){} 
} 

class SetVisibilityFilter { 
  constructor(public filter: string){} 
} 

type Action = AddTodoAction | 
              ToggleTodoAction | 
              SetVisibilityFilter;
```

Action类型，它是三个 actions 的并集，代表了应用程序将能够执行的任何操作。

#### Observable

我们需要定义将返回应用程序状态的可观察状态的状态函数。为了使这个例子更像真实项目，我将把状态函数分为两部分：一个处理todos，另一个处理可见性过滤器。

让我们先来谈谈todos。

```
function todos(initState: Todo[], actions: Observable<action>): Observable<todo> { 
      return actions.scan((state, action) => { 
        if (action instanceof AddTodoAction) { 
          const newTodo = {
            id: action.todoId, 
            text: action.text, 
            completed: false
          }; 
          return [...state, newTodo]; 
        } else { 
          return state; 
        } 
     }, initState); 
}
```

我想指出有一些有趣的事情。

首先看功能的签名。该函数不采取单一动作，而是一个 *observable* 的RxJS  *actions*。同样，它不会返回一个todos列表，而是一个 observable。

第二，actions.scan将累加器函数应用于可观察序列，并返回每个中间结果。所以在每个动作之后都会发出一个新的todos列表。

第三，*TypeScript realizes* 到if子句中的动作是一个AddTodoAction。这意味着我可以访问todoId和文本属性，但不能过滤。 这使我能够以类型安全的方式写入这些功能。 这是非常好的，因为这些功能是您的应用程序的智慧居住的地方，因此，它们很快变得不平凡。 所以有一些编译器支持有一个很大的好处。 如果您是Redux用户，并且您习惯了

```
{type: 'toggle', id: 15}
```

代替

```
new ToggleTodoAction(15)
```

你很幸运，TypeScript 2.0支持区分式联合类型，因此您可以安全地输入类似Redux的代码。

接下来，我们通过添加一个todo切换功能来扩展todos。

```

function todos(initState: Todo[], actions: Observable<action>): Observable<todo> { 
    return actions.scan((state, action) => { 
      if (action instanceof AddTodoAction) { 
        const newTodo = {
          id: action.todoId, 
          text: action.text, 
          completed: false
        }; 
        return [...state, newTodo]; 
      } else { 
        return state.map(t => updateTodo(t, action)); 
      } 
    }, initState); 
} 

function updateTodo(todo: Todo, action: Action): Todo { 
  if (action instanceof ToggleTodoAction) { 
    // merge creates a new object using 
    // the properties of the passed in objects 
    return (action.id !== todo.id) ? 
      todo : 
      merge(todo, {completed: !state.completed}); 
  } else { 
    return todo; 
  } 
}
```

类似于todos，我们可以实现一个创建observable过滤器的可视化功能的函数。

```
function filter(initState: string, actions: Observable<action>): Observable<string> { 
   return actions.scan((state, action) => { 
     if (action instanceof SetVisibilityFilter) { 
       return action.filter; 
     } else { 
       return state; 
     } 
   }, initState); 
}
```

最后，我们可以将它们结合起来来创建stateFn。

```
function stateFn(initState: AppState, actions: Observable<action>): Observable<appstate> { 
  const combine = s => ({todos: s[0], visibilityFilter: s[1]});
  const appStateObs: Observable<appstate> = 
    todos(initState.todos, actions).   
    zip(filter(initState.visibilityFilter, actions)).
    map(combine); 
  return wrapIntoBehavior(initState, appStateObs); 
}
```

这六行代码中有很多事情发生。

首先，我们使用上面定义的函数创建todos和过滤器。然后，我们将它们压缩成可观察的对，我们将其映射到AppState中。

这个observable有一个问题。如果一个组件订阅它，组件将不会收到任何数据，直到observable发出一个新的事件。为此，必须发出新的action。这不是我们想要的。我们想要的是组件在订阅的那一刻接收最新的快照。这就是BehaviorSubject的作用。

```
function wrapIntoBehavior(init, obs) { 
  const res = new BehaviorSubject(init); 
  obs.subscribe(s => res.next(s)); 
  return res; 
}
```

行为主体是可观察的，将向每个新订阅者发出最新的值。

为了更好地了解stateFn的工作原理，我们来写一些单元测试：

```
it('should create a new todo', () => { 
  const actions = new Subject<action>(); 
  const states = stateFn(
    {todos: [], visibilityFilter: 'SHOW_ALL'}, actions); 

  actions.next(new AddTodoAction(100, 'todo1')); 
  actions.next(new AddTodoAction(101, 'todo2'));
 
  states.subscribe(s => { 
    expect(s.todos.length).toEqual(2);
    expect(s.todos[0]).toEqual({
      id: 100, text: 'todo1', completed: false});    
    expect(s.todos[1]).toEqual({
      id: 101, text: 'todo2', completed: false});
  }); 
}); 



it('should toggle a todo', () => { 
  const actions = new Subject<action>(); 
  const todos = [ 
    {id: 100, text: 'todo1', completed: false}, 
    {id: 101, text: 'todo2', completed: false}
  ]; 
  const states = stateFn(
    {todos: todos, visibilityFilter: 'SHOW_ALL'}, actions); 

  actions.next(new ToggleTodo(100)); 

  states.subscribe(s => { 
    expect(s.todos[0].completed).toBeTrue();      
    expect(s.todos[1].completed).toBeFalse(); 
  }); 
});
```

**如果您熟悉Redux，可以找到stateFn函数与Redux reducer相似。但实际上有一个很大的区别：stateFn函数只被调用一次，而每个动作都调用Redux reducer。**

这很重要，原因如下：

**Just an Observable**

**stateFn函数只调用一次来创建状态observable。应用程序的其余部分（例如，角度2组件）不必知道stateFn甚至存在。**他们所关心的只是 observable。这给了我们如何实现这个功能的很大的灵活性。在这个例子中，我们以像Redux这样的方式。但是我们可以改变它，而不影响应用程序中的其他任何东西。此外，由于Angular 2已经配有RxJS，所以我们没有必要引入任何新的库。

**同步和异步**

**在这个例子中，stateFn是同步的。但是由于可观察是基于推式的，我们可以引入异步，而不需要更改函数的公共API。** 所以我们可以使一些 action 处理同步和一些异步，而不影响任何组件。 随着应用程序的增长，当在web-worker或服务器中执行越来越多的 actions 时，这一点非常重要。 使用基于push的集合的一个缺点是它们可能很难使用，但Angular 2提供了一个原始的 - 异步管道 - 这有助于此。

**强大的 RxJS**

RxJS配备了很多强大的组合器，可以简单和声明的方式实现复杂的交互。 例如，当动作A被发出时，应用程序应该等待动作B然后发出新的状态。 然而，如果动作B在五秒内没有发出，则应用程序应发出错误状态。 您可以使用RxJS在几行代码中实现。

**这很关键。 应用程序状态管理的复杂性来自于协调这种交互。 一个强大的工具，如RxJS，它负责很多协调逻辑，可以大大降低状态管理的复杂性。**

#### 应用和视图边界

在这一点上，我们还没有写任何Angular特定的代码，我们还没有编写单个组件。 这是这种架构的优点之一 - 应用程序和视图逻辑是分开的。 但他们如何沟通？

他们通过 dispatcher 和 state  对象进行通信。

![img](https://cdn-images-1.medium.com/max/800/0*RPjx0LDcpnKlkOtR.jpg)

我们可以用以下方式创建它们：

```
const initState = new OpaqueToken("initState"); 
const dispatcher = new OpaqueToken("dispatcher"); 
const state = new OpaqueToken("state"); 

const stateAndDispatcher = [ 
  {
     provide: initState, 
     useValue: {todos: [], visibilityFilter: 'SHOW_ALL'}
  }, 
  {
    provide: dispatcher, 
    useValue: new Subject<action>(null)
  }, 
  {
    provide: state,
    useFactory: stateFn,
    deps: [initState, dispatcher]
  }
];
```

- dispatcher 是一个RxJS subject，这意味着它既是可观察的，也是观察者。 所以我们可以把它传递给stateFn，并用它来发出动作。
- state 是 stateFn 函数返回的 observable 。

我们可以注册这样的providers ：

```
@Component({ 
  selector: 'todo-app', 
  template: `...`, 
  providers: stateAndDispatcher 
}) 
class TodoApp {}
```

然后将它们注入到组件。

```
@Component(...) 
class FilterLink { 
  constructor(@Inject(state) private state: Observable<appstate>){} 
}
```

**没有 Store**

请注意，与Redux或Flux相反，没有 store。dispatcher 只是一个RxJS观察者，state 只是一个 observable。 这意味着我们可以使用内置的RxJS组合器来改变这些对象的行为，提供mocks等。

**没有全局对象**

因为我们使用依赖注入来注入state 和dispatcher，而这些是两个单独的对象，所以我们可以很容易的对它们进行装饰。 例如，我们可以覆盖组件子树中的 dispatcher provider ，以便仅记录来自该子树的所有发出的操作。 或者我们可以包装dispatcher 来自动调整所有操作，当多个团队在同一个应用程序上工作时，这可以非常方便。 我们也可以装饰state provider ，例如启用去抖动。

#### 视图

最后，我们得到最有趣的部分 - 实现视图层。

**显示Todos**

我们从一个渲染一个todo的组件开始。

```
@Component({ 
  selector: 'todo', 
  template: `<span> {{text}} </span>` 
}) 
class Todo { 
  @Input() text: string; 
  @Input() completed: boolean; 
  @Output() toggle = new EventEmitter(); 
  get textEffect() { 
    return this.completed ? 'line-through' : 'none'; 
  } 
}
```



这就是丹·阿布拉莫夫所说的一个愚蠢的或表现的组成部分。 这个组件不知道应用程序的一些事情。 它只知道如何渲染一个todo。

接下来，todo列表组件。

```
@Component({ 
  selector: 'todo-list', 
  template: `<todo *ngFor="let t of filtered"></todo>`
}) 
class TodoList { 
  constructor(
    @Inject(dispatcher) private dispatcher: Observer<action>,
    @Inject(state) private state: Observable<appstate>) {} 

  get filtered() { 
    return this.state.map(s => 
      getVisibleTodos(s.todos, s.visibilityFilter)); 
  } 

  emitToggle(id) { 
    this.dispatcher.next(new ToggleTodoAction(id)); 
  } 
}
```



我们在这里做的第一件事是我们注入state创建一个可观察的todos过滤器。 由于observables 是基于push的，它们可能会变得非常困难。 我们在角色团队中认识到这一点，因此，为什么要提供一些让事情更容易的事情。 例如，异步管道“提取” observable 的最后一个值。 这允许我们在需要该对象的任何地方使用observable的对象。

其次，我们使用 injected dispatcher 在emitToggle事件处理程序中发出一个新的动作。

该组件知道应用程序，因为它注入dispatcher 和state。 有些人可以说这个组件混合了表现和非表现的关注。 所以如果你觉得很强，你可以把这个组件分成两部分。 但我不知道是否会买你这么多。**Angular 组件已将表现层分为模板。** 将每个这样的组件分成两个可能是没必要的。

**Adding Todos**

Next, let’s create a component adding todos.

```
@Component({ 
  selector: 'add-todo', 
  template: `<input><button>Add Todo</button>` 
}) 
class AddTodo { 
  constructor(@Inject(dispatcher) private dispatcher: Observer<action>) {} 

  addTodo(value) { 
    this.dispatcher.next(new AddTodoAction(nextId++, value)); 
  } 
}
```



**Filtering Todos**

Now, let’s add an ability to filter todos:

```
@Component({ 
  selector: 'filter-link', 
  template: `<a href="#"><ng-content></ng-content></a>` 
}) 
class FilterLink { 
  @Input() filter: string; 

  constructor(
    @Inject(dispatcher) private dispatcher: Observer<action>, 
    @Inject(state) private state: Observable<appstate>){} 

  get textEffect() { 
    return this.state.map(s => 
      s.visibilityFilter === this.filter ? 'underline' : 'none'); 
  } 

  setVisibilityFilter() { 
    this.dispatcher.next(new SetVisibilityFilter(this.filter)); 
  } 
} 

@Component({ 
  selector: 'footer', 
  template: `
   <filter-link filter="SHOW_ALL">All</filter-link>
   <filter-link  filter="SHOW_ACTIVE">Active</filter-link>
   <filter-link filter="SHOW_COMPLETED">Completed</filter-link>`
}) 
class Footer {}
```



**Root Component**

Finally, we add a root component combining different parts into our application.

```
@Component({ 
  selector: 'ng-demo', 
  template: `
    <add-todo></add-todo>
    <todo-list></todo-list>
    <footer></footer> 
  `, providers: stateAndDispatcher 
}) 
class TodoApp {}
```



------

### 它很快！

**所描述的模式不仅使Angular应用程序更容易组织和重构。 它也使它更加高效。** 这是因为当应用程序状态被存储为不可变的数据结构时，state 的变化被表示为observable，所以我们可以设置所有Angular 组件的OnPush策略。 要了解更多信息，请阅读[Angular, Immutability, and Ecapsulation](http://victorsavkin.com/post/133936129316/angular-immutability-and-encapsulation)。

### 其他状态管理方式

当然，这不是在Angular应用程序中管理应用程序状态的唯一方法。 例如，您可以使用 [@ngrx/store](http://t.umblr.com/redirect?z=https%3A%2F%2Fgithub.com%2Fngrx%2Fstore&t=YjE2ODY0NWY1NDdlMDdiYTYzNzdjMTE2YzBmMDFkNGVmM2UxNzBjOSxpUEdGS2xFVg%3D%3D)。 您还可以编写不使用不可变数据或可观察值的应用程序，而是使用用例服务或DCI。

### 总结

多个后端， web workers和UI组件之间的协调使应用状态管理成为一项极具挑战性的任务。 像Redux和Flux这样的模式有助于解决这个问题。 在本文中，我使用RxJS显示了在几行代码中实现类似模式的简单性。 然后我展示了如何使用它来实现一个简单的Angular 2应用程序。