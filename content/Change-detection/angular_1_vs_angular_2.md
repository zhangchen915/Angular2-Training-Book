#Angular.js 与 Angular 中的变化检测策略

框架的两个版本之间的另一个区别是应用程序（指令或组件）检查DOM节点是否需要更新的方式。

由于双向数据绑定的本质，在Angular.js中不能保证父节点在子节点之前总是被检查。 有可能子节点可以改变父节点或兄弟节点或树中的任何其他节点，这又会在链中触发新的更新。 这使得变化检测机制难以遍历所有节点，并可能掉入具有臭名昭着的“震荡”循环中：

```
10 $digest() iterations reached. Aborting!
```

译注：这是Angular.js中的当两个方法互相watch时，就会导致不停地进行digest循环，当循环大于十次Angular就会抛出如上错误。

在Angular 2中，改变被保证单向传播。 更改检测器将遍历每个节点一次，**始终从根开始**。 这意味着父组件始终在其子组件之前检查。

*Tree traversing in Angular 1 vs Angular 2*

![File Structure](../assets/angular1-vs-angular2.jpg)

Figure: File Structure



❗不要**违背单向数据流策略** ，这会导致：

- 数据和视图处于不一致状态会导致应用程序难以推理
- 多次重复该过程等待数据稳定可能会导致性能问题