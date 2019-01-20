# Redux 和 Ngrx

## 什么是 Redux?

Redux是JavaScript应用的状态管理器，并通过在应用程序中具有单向数据流来保持Flux架构的核心原理。

在 Flux 应用程序传统上拥有多个存储的地方，Redux应用程序只有一个全局只读应用程序状态。 该状态通过“reducing”一个集合或动作流来计算实现以可控的方式更新。

## What is Ngrx?

Redux的状态管理很受欢迎，并且启发了[ @ngrx](https://github.com/ngrx)的创建，@ngrx是一套实现与Redux生态系统相同的管理状态以及一些中间件和工具的模块。 @ngrx被创建为与Angular和RxJS专门使用，因为它严重依赖于可观察的范例。

我们将在应用程序中描述如何使用这种方法。

有关Redux和@ngrx的更多信息，请参阅进一步阅读部分。