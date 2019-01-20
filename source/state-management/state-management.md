# 状态管理

对于具有大量异步活动以及在多个组件和模块之间有很多共享状态的大型Angular应用程序，管理状态可能相当具有挑战性。 在典型的应用中，我们管理的东西如下：

- 来自服务器的数据以及是否挂起或导致错误
- UI状态如切换，警报和错误消息
- 用户输入，如表单提交，过滤器和搜索查询
- 自定义主题，凭据和本地化
- 许多其他类型的状态

随着应用程序的发展，我们如何知道一个模块中的状态变化将一致而准确地反映在其他模块中？ 如果这些修改导致更多的状态更改呢？ 最终，您的应用程序中实际发生的事情变得非常困难，并导致大量的bug。

在Angular，主要有三种的方法来解决这个问题。

1. [Redux using @ngrx](https://angular-2-training-book.rangle.io/handout/state-management/ngrx/)
2. Redux using ng2-redux
3. Angular Services and RxJS（推荐）