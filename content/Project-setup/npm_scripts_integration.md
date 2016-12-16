# NPM 脚本集成

NPM允许我们在`package.json`文件中定义自定义脚本。然后，这些可以使用NPM CLI执行任务。

我们依靠这些脚本来管理大多数项目任务以及适配webpack。

 这些脚本在`package.json`文件的`scripts`属性中定义。例如：

```
...
  scripts: {
    "clean": "rimraf dist",
    "prebuild": "npm run clean",
    "build": "NODE_ENV=production webpack",
  }
...
```

NPM允许前后任务绑定，方法是先将单词`pre`或`post`分别附加到任务名称。 

这里，我们的预构建`prebuild`任务在我们的`build`任务之前执行。

> 我们可以从另一个NPM脚本内部运行NPM脚本。

要调用构建脚本，我们运行命令`npm run build`：

1. `prebuild`任务执行；
2. `prebuild`任务运行`clean`任务，执行`rimraf dist`命令；
3. `rimraf`（命令来自NPM包）递归删除指定文件夹中的所有内容；
4. 执行构建任务。 这会将`NODE_ENV`环境变量设置为`production` ，并启动webpack构建；
5. Webpack根据项目根文件夹中可用的`webpack.config.js`生成打包文件。