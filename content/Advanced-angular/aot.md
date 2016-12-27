# Angular 2 的 AoT

每个Angular应用程序需要一个编译过程才能在浏览器中运行：Angular提供的丰富的组件和模板不能被浏览器直接理解。 在编译期间，Angular的编译器还通过考虑JavaScript VM的功能（如内联缓存）来提高应用程序运行时性能。

Angular 1.x和Angular 2中的初始编译器称为JiT（Just-in-Time）编译器。 对于AoT，它代表最近在Angular 2中引入的Ahead-of-Time编译器。与在运行时由Angular 2执行的JiT编译相比，AoT提供了一个较小的包，在浏览器中更快的呈现。 使用AoT，与 [angular2-starter](https://github.com/rangle/angular2-starter/pull/149) 原始的1.2 MB相比，我们可以减少428.8 kb，并通过跳过浏览器中的编译减少加载时间。

| Characteristic        | JiT            | AoT               |
| --------------------- | -------------- | ----------------- |
| Compilation target    | Browser        | Server            |
| Compilation context   | Runtime        | Build             |
| Bundle size           | Huge (~1.2 MB) | Smaller (~400 KB) |
| Execution Performance | -              | Better            |
| Startup time          | -              | Shorter           |

AoT的要点是将编译从运行时移动到构建过程。这意味着，首先我们可以从捆绑包中删除JiT编译器（大约523kb）以具有更小的构建，其次，浏览器可以执行代码，而无需在运行时等待JiT，这让渲染速度更快。

早期编译还意味着开发人员可以在实际运行代码之前和到达客户端之前找到模板错误。这提供了更强大的应用程序具有更高的安全性，因为更少的客户端HTML和JavaScript被`eval`。此外，通过在构建过程中引入编译代码，AoT使应用程序更具tree-shakable性，并可开放于各种其他优化。像Rollup和Google Closure这样的Bundler可以发挥这种优势，有效地减少包大小。

此外，AoT编译器还内联HTML模板和CSS文件，并帮助减少应用程序发送的异步请求数量。（注意：这导致了一个配置错误，我们将在后面的部分提到）