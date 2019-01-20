# 定义应用的主状态

当使用Redux构建应用程序时，首先要考虑的是“我要存储什么状态”？ 捕获所有应用程序的状态通常是一个好主意，以便在任何地方都可以访问，而且都在同一位置方便检查。

在应用程序state中，我们存储的东西如下：

- 通过调用API收到的数据
- 用户输入
- 视图状态，如菜单和按钮切换
- Application preferences
- 国际化信息
- 主题和其他可定制的应用领域

要定义应用程序的state，请使用名为`AppState`或`IAppState`的接口，这取决于项目中使用的命名约定。

示例：

*app/models/appState.ts*

```typescript
export interface AppState {
  readonly colors: Colors;
  readonly localization: Localization;
  readonly login: Login;
  readonly projectList: ProjectList;
  readonly registration: Registration;
  readonly showMainNavigation: boolean;
}
```

> **注意：**我们使用readonly来确保编译时不可变性，并且它提供了最简单的不可变实现，而不增加更多的依赖性来混淆示例。 但是，您可以自由地选择其他方法。