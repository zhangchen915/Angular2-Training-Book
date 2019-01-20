# 执行测试脚本

我们的整个测试工作流程是通过Karma完成的。 运行命令`karma start`来启动Karma来设置测试环境，运行每个单元测试并执行我们在`karma.config.js`配置文件中设置的任何记录器。 为了通过命令行运行Karma，它必须全局安装（`npm install karma -g`）。
一个好的做法是合并所有项目的任务/构建命令通过npm。 这为您的构建过程提供了连续性，并使人们更容易测试/运行应用程序，而无需了解您的确切技术堆栈。 在`package.json`中，`scripts`字段包含具有键值配对的对象，其中键是命令的别名，值是要执行的命令。

```
...
"scripts": {
    "test": "karma start",
    ...
}
...

```

现在运行`npm test`将启动Karma。下面是我们的Karma测试的输出。如您所见，我们有一个测试已通过，在Chrome 48浏览器中运行。



![image](https://angular-2-training-book.rangle.io/handout/images/simple-output.png)