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

# 示例

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

**The stateFn function is called only once to create the state observable. The rest of the application (e.g., Angular 2 components) do not have to know that stateFn even exists. **All they care about is the observable. This gives us a lot of flexibility in how we can implement the function. In this example, we did it in a Redux-like way. But we can change it without affecting anything else in the application. Also, since Angular 2 already ships with RxJS, we did not have to bring in any new libraries.

**Synchronous and Asynchronous**

**In this example, stateFn is synchronous. But since observables are push-based, we can introduce asynchronicity without changing the public API of the function.** So we can make some action handlers synchronous and some asynchronous without affecting any components. This gets important with the growth of the application, when more and more actions have to be performed in a web-worker or server. One downside of using push-based collections is that they can be awkward to use, but Angular 2 provides a primitive — the async pipe — that helps with that.

**Power of RxJS**

RxJS comes with a lot of powerful combinators, which enables implementing complex interactions in a simple and declarative way. For instance, when action A gets emitted, the application should wait for action B and then emit a new state. If however, the action B does not get emitted in five seconds, the application should emit an error state. You can implement this in just a few lines of code using RxJS.

**This is key. The complexity of application state management comes from having to coordinate such interactions. A powerful tool like RxJS, which takes care of a lot of coordination logic, can dramatically decrease the complexity of the state management.**

#### Application and View Boundary

At this point we have not written any Angular-specific code yet, we have not written a single component. This is one of the benefits of this architecture — the application and the view logic are separated. But how do they communicate?

They communicate via the dispatcher and state objects.

![img](https://cdn-images-1.medium.com/max/800/0*RPjx0LDcpnKlkOtR.jpg)

We can create them as follows:

- dispatcher is a RxJS subject, which means that it is both an observable and an observer. So we can pass it into stateFn, and use it to emit actions.
- state is an observable returned by the stateFn function.

We can register the providers like this:

And then inject them into components.

**No Store**

Note, that in opposite to Redux or Flux, there is no store. The dispatcher is just an RxJS observer, and state is just an observable. This means that we can use the built-in RxJS combinators to change the behavior of these objects, provide mocks, etc.

**No Global Objects**

Because we use dependency injection to inject the state and the dispatcher, and those are two separate objects, we can easily decorate them. For instance, we can override the dispatcher provider in a component subtree to log all the emitted actions from that subtree only. Or we can wrap the dispatcher to automatically scope all actions, which can be very handy when multiple teams are working on the same application. We can also decorate the state provider to, for instance, enable debouncing.

#### View

Finally, we got to the most interesting part — implementing the view layer.

**Displaying Todos**

Let’s start with a component rendering a single todo.

This is what Dan Abramov calls a dumb or presentational component. This component is not aware of the application side of things. It only knows how to render a todo.

Next, the todo list component.

The first thing we do here is we create an observable of filtered todos using the injected state. Since observables are push-based, they can be awkward to work with. We in the Angular team recognize this, and, that is why, provide a few things to make it easier. For instance, the async pipe “extracts” the last value of an observable. This allows us to use an observable of an object in any place where that object is required.

Second, we use the injected dispatcher to a emit new action in the emitToggle event handler.

This component is aware of the application because it injects the dispatcher and the state. Some can say this component mixes presentational and non-presentational concerns. So if you feel strong about it, you can separate this component into two. But I am not sure if it will buy you that much. **Angular components already separate presentational aspects into a template. **Splitting every such component into two might be an overkill.

**Adding Todos**

Next, let’s create a component adding todos.

**Filtering Todos**

Now, let’s add an ability to filter todos:

**Root Component**

Finally, we add a root component combining different parts into our application.

------

### It is fast!

**The pattern described does not just make Angular applications easier to organize and refactor. It also makes it more performant. **This is because when the application state is stored as an immutable data structure, and the changes in the state are represented as an observable, we can set the OnPush strategy for all the Angular components. To learn more about it, read [Angular, Immutability, and Ecapsulation](http://victorsavkin.com/post/133936129316/angular-immutability-and-encapsulation).

### Other ways to manage state

Of course, this is not the only way to manage application state in Angular apps. For instance, you can use [@ngrx/store](http://t.umblr.com/redirect?z=https%3A%2F%2Fgithub.com%2Fngrx%2Fstore&t=YjE2ODY0NWY1NDdlMDdiYTYzNzdjMTE2YzBmMDFkNGVmM2UxNzBjOSxpUEdGS2xFVg%3D%3D). You can also write applications that do not use immutable data or observables, and instead use use-case services or DCI.

### Summary

Coordinating between multiple backends, web workers, and UI components is what makes managing application state such a challenging task. Patterns like Redux and Flux help address this. In this article, I showed how easy it is to implement a similar pattern in just a few lines of code using RxJS. Then I showed how we can use it to implement a simple Angular 2 application.