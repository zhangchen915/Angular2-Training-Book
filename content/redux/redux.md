# Redux 和 Ngrx

## 什么是 Redux?

Redux is an application state manager for JavaScript applications, and keeps with the core principles of the Flux-architecture by having a unidirectional data flow in your application.

Where Flux applications traditionally have multiple stores, Redux applications have only one global, read-only application state. This state is calculated by "reducing" over a collection or stream of actions that update it in controlled ways.

One popular Angular 2 specific implementation of the Redux pattern is [Ng2-Redux](https://github.com/wbuchwalter/ng2-redux), which we'll be using to describe how to use this approach in an application.

Redux是JavaScript应用程序的应用程序状态管理器，通过在应用程序中使用单向数据流，遵循Flux体系结构的核心原则。
当Flux应用程序传统上具有多个存储时，Redux应用程序只有一个全局只读应用程序状态。 此状态通过“减少”在受控方式更新它的动作集合或流的计算。
Redux模式的一个流行的Angular 2特定实现是Ng2-Redux，我们将用它来描述如何在应用程序中使用这种方法。

## What is Ngrx?

Redux implementation has been very well received and has inspired the creation of [ngrx](https://github.com/ngrx), a set of modules that implement the same way of managing state as well as some of the middleware and tools in the Redux ecosystem. Ngrx was created to be used specifically with Angular 2 and [RxJS](https://github.com/Reactive-Extensions/RxJS), as it leans heavily on the observable paradigm.

Although we'll be using Ng2-Redux, a lot of the same lessons apply with regards to ngrx though the syntax may be different and have some slight differences in what abstractions are involved.

*For further on Redux and ngrx see the Further reading section*