# 安装

引入webpack及其插件的最简单的方法是通过NPM并将其保存到您的`devDependencies`：

```
npm install -D webpack ts-loader html-webpack-plugin tslint-loader

```

# 设置和使用

使用webpack的最常见方法是通过CLI。默认情况下，运行命令会执行`webpack.config.js`，这是webpack设置的配置文件。

## Bundle

webpack的核心概念是bundle。 bundle是一个简单的模块集合，我们定义它们如何分离的边界。 在这个项目中，我们有两个包：

- `app` 为我们的应用程序特定的客户端逻辑
- 第三方库的`vendor` 

在webpack中，通过入口点配置bundle。 Webpack逐个遍历每个入口点。 它通过遍历每个模块的引用映射一个依赖关系图。 它遇到的所有依赖关系然后打包到该捆绑包中。
通过NPM安装的软件包使用CommonJS的模块化方式引入。 在JavaScript文件中，它将如下所示：

```
  const app = require('./src/index.ts');

```

或在TypeScript/ES6 文件中：

```
  import { Component } from '@angular/core';

```

我们将使用这些字符串值作为我们传递给webpack的模块名称。

 让我们来看看我们在示例应用中定义的入口点：

```json
{
  ...
  entry: {
    app: './src/index.ts',
    vendor: [
      '@angular/core',
      '@angular/compiler',
      '@angular/common',
      '@angular/http',
      '@angular/platform-browser',
      '@angular/platform-browser-dynamic',
      '@angular/router',
      'es6-shim',
      'redux',
      'redux-thunk',
      'redux-logger',
      'reflect-metadata',
      'ng2-redux',
      'zone.js',
    ]
  }
  ...
}
```

app的入口点，./src/index.ts，是我们Angular 2应用程序的基本文件。 如果我们正确地定义了每个模块的依赖关系，这些引用应该连接我们应用程序的所有部分。`vendor` 的入口点是我们的应用程序代码正常工作所需的模块列表。 即使这些文件被我们的应用程序包中的某个模块引用，我们也希望将这些资源分离在第三方代码中。

## 输出配置

在大多数情况下，我们不只是想要配置webpack如何生成bundle - 我们还想配置如何输出这些bundle。

* 通常，我们将要重新路由保存文件的位置。 例如，进入`bin`或`dist`文件夹。 这是因为我们想要优化我们的生产。
* Webpack在绑定我们的模块并输出它们时转换代码。 我们想要有一种方法来连接由webpack生成的代码和我们编写的代码。
* 服务器路由可以以许多不同的方式配置。 我们可能想要一些配置webpack的方式来考虑我们的服务器路由设置。

所有这些配置选项都由config的`output`属性处理。 让我们看看我们如何设置我们的配置来解决这些问题：

```json
{
  ...
  output: {
    path: path.resolve(__dirname, 'dist'),
    filename: '[name].[hash].js',
    publicPath: "/",
    sourceMapFilename: '[name].[hash].js.map'
  }
  ...
}
```

> 一些选项包含在方括号中的单词。 Webpack能够解析这些属性的参数，每个属性都有一组不同的可用于替换的参数。在这里，我们使用`name（`包名称）和`hash（`包的内容的哈希值）。

要将捆绑文件保存在不同的文件夹中，我们使用path属性。这里，`path`告诉webpack所有的输出文件必须保存到`path.resolve(__ dirname，'dist')`。在我们的例子中，我们将每个bundle保存到一个单独的文件中。此文件的名称由`filename`属性指定。

链接这些捆绑的文件和我们实际编码的文件是使用所谓的源映射。有不同的方式配置源地图。我们想要的是将这些源映射保存在由`sourceMapFilename`属性指定的单独文件中。服务器访问文件的方式可能不会直接跟随文件系统树。对我们来说，我们要使用dist下保存的文件作为我们服务器的根文件夹。为了让webpack知道这一点，我们将`publicPath`属性设置为`/`。