#Linting
许多编辑支持“linting”的概念 - 计算机程序的语法检查。 可以在程序员的编辑器中和/或通过自动化来完成。

对于TypeScript有一个名为tslint的包（npm install --save-dev tslint），可以插入许多编辑器。 tslint也可以配置一个tslint.json文件。
Webpack可以在尝试运行tsc之前运行tslint。 这是通过安装tslint-loader（npm install --save-dev tslint-loader）来实现的，它插入到webpack中，如下所示：

```js
// ...
module: {
  preLoaders: [
    { test: /\.ts$/, loader: 'tslint' }
  ],
  loaders: [
    { test: /\.ts$/, loader: 'ts', exclude: /node_modules/ },
    // ...
  ]
  // ...
}
```