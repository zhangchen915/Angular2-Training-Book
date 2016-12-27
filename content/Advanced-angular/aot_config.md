# AoT Configuration

要在Angular 2中启用AoT，有两种可能的方法：

- 直接使用`ngc` 
- 使用`@ngtools/webpack`

我们推荐第二种方式，因为它最适合Angular + Webpack工具链。 One problem of using raw `ngc` is that `ngc` tries to inline CSS while lacking necessary context. For example, the `@import 'basscss-basic'` statement in `index.css` would cause an error like `Error: Compilation failed. Resource file not found` with `ngc`. It lacks the information that `'basscss-basic'` is actually a node module inside `node_modules`. On the other hand, `@ngtools/webpack` provides `AotPlugin` and loader for Webpack which shares the context with other loaders/plugins. So when `ngc` is called by `@ngtools/webpack`, it can gather necessary informations from other plugins like `postcss-import` to correctly understand things like `@import 'basscss-basic'`.

使用原始`ngc`的一个问题是`ngc`会尝试内联CSS，而缺少必要的上下文。 例如，`index.css`中的`@import 'basscss-basic'`语句将导致类似于`Error: Compilation failed. Resource file not found`。 它缺少`“basscss-basic”`实际上是`node_modules`内部的节点模块的信息。 另一方面，`@ngtools/webpack`提供了`AopPlugin`和与其他加载器/插件共享上下文的Webpack加载器。 所以当ngc由`@ngtools/webpack`调用时，它可以从其他插件收集必要的信息，如`postcss-import`，以正确理解像`@import 'basscss-basic'`。

## 配置`@ngtools/webpack`

首先，从npm获取`@ngtools/webpack`并将其保存为开发依赖关系：

```
npm install -D @ngtools/webpack

```

然后，在Webpack配置文件（通常命名为`webpack.config.js`）中，添加以下代码：

```javascript
import {AotPlugin} from '@ngtools/webpack'

exports = { /* ... */
  module: {
    rules: [
      {
        test: /\.ts$/,
        loader: '@ngtools/webpack',
      }
    ]
  },

  plugins: [
    new AotPlugin({
      tsConfigPath: 'path/to/tsconfig.json',
      entryModule: 'path/to/app.module#AppModule'
    })
  ]
}
```

这里`@ngtools/webpack`替换了其他类型的脚本加载器，如`ts-loader`或`awesome-typescript-loader`。 它与`AotPlugin`一起工作以启用AoT编译。 更多细节可以在 [这里](https://github.com/angular/angular-cli/tree/master/packages/webpack)找到。
（注意，对于由angular-cli生成的项目，打开AoT可以很简单，因为ng build --aot，但是由于angular-cli不允许为复杂的用例定制webpack配置，它可能不够）。