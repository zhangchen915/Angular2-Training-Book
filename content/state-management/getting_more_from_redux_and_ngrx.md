# 更多Redux和Ngrx资源

- ## Redux

  Redux在其生态系统中提供了许多工具和中间件，以促进优雅的应用程序开发。

  - *[Redux DevTools](https://github.com/gaearon/redux-devtools)* - 该工具显示与其存储交互的操作的线性时间线。允许重放操作和错误处理
  - *[redux-thunk](https://github.com/gaearon/redux-thunk)* - 实现动作延迟评估的中间件
  - *[redux-observable](https://github.com/redux-observable/redux-observable)* - 基于RxJS的模型用于处理action流的副作用。
  - *[ng2-redux-router](https://github.com/dagstuan/ng2-redux-router) - Angular 2路由和你的redux store之间的reactive glue

  ## Ngrx

  Ngrx通过 [ngrx/store](https://github.com/ngrx/store) 模块提供了大多数Redux实现。其他模块可用于更好的集成和开发。

  - *[ngrx/store-devtools](ngrx/store-devtools)* - Redux DevTools的ngrx实现
  - *[ngrx/effects](ngrx/effects)* - 用于执行类似于`redux-saga`的副作用的模型
  - *[ngrx/router](ngrx/router)* and *[ngrx/router-store](ngrx/router-store)* - 用于Angular 2的路由，可以连接到您的ngrx store