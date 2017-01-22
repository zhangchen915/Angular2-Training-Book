# Loaders

TypeScript不是核心JavaScript，所以webpack需要一点额外的帮助来解析`.ts`文件。 它通过使用装载器来做到这一点。 加载器是一种配置webpack如何转换我们的bundle中的特定文件的输出的方法。 我们的`ts-loader`包能够处理TypeScript文件的这种转换。

## Inline

在需要/导入模块时，可以内联配置装载程序：

```
const app = require('ts!./src/index.ts');

```

加载器通过使用`!`字符来分隔模块引用和将要运行的加载器。可以使用多个加载器，并且它们以同样用`!`分开。装载机从右到左执行。

```
const app = require('ts!tslint!./src/index.ts');

```

> 虽然软件包命名为`ts-loader`，`tslint-loader`，`style-loader`，我们不需要在我们的配置中包含`-loader`部分。

在以这种方式配置加载器时要小心 - 它将应用程序不同阶段的实现细节结合在一起，因此在许多情况下可能不是正确的选择。

## Webpack 配置

首选方法是通过webpack.config.js文件配置装载程序。例如，TypeScript加载器任务看起来像这样：

```
{
  test: /\.ts$/,
  loader: 'ts-loader',
  exclude: /node_modules/
}

```

这将运行typescript编译器，它遵循上面指定的配置设置。 我们希望能够处理其他文件，而不仅仅是TypeScript文件，所以我们需要指定一个加载器列表。 这是通过创建任务数组来完成的。
此数组中指定的任务被链接。 如果文件匹配多个条件，则将按顺序使用每个任务处理它。

```js
{
  ...
  module: {
    rules: [
      { test: /\.ts$/, loader: 'tslint' },
      { test: /\.ts$/, loader: 'ts', exclude: /node_modules/ },
      { test: /\.html$/, loader: 'raw' },
      { test: /\.css$/, loader: 'style!css?sourceMap' },
      { test: /\.svg/, loader: 'url' },
      { test: /\.eot/, loader: 'url' },
      { test: /\.woff/, loader: 'url' },
      { test: /\.woff2/, loader: 'url' },
      { test: /\.ttf/, loader: 'url' },
    ],
    noParse: [ /zone\.js\/dist\/.+/, /angular2\/bundles\/.+/ ]
  }
  ...
}
```

每个任务都有一些配置选项：

* test - 文件路径必须匹配此条件才能处理。 这通常用于测试文件扩展名。 `/\.ts$/`。
* loader - 将用于转换输入的装载程序。 这遵循上面指定的语法。
* exclude - 文件路径不能与要处理的此条件匹配。 这通常用于排除文件夹，例如。 `/ node_modules /`。
* include - 文件路径必须与要处理的此条件匹配。 这通常用于包括文件夹。 例如。 `path.resolve(__ dirname,'app / src')`。

### Pre-Loaders

preLoaders数组的工作方式就像加载器数组一样，它是一个单独的任务链，在loader任务链之前执行。

### 非 JavaScript 资源

Webpack还允许我们加载非JavaScript资源，例如：CSS，SVG，字体文件等。为了将这些资源附加到我们的包中，我们必须在我们的应用程序模块中导入它们。 例如：

```js
import './styles/style.css';

// or

const STYLES = require('./styles/style.css');
```

### 其他常用 Loaders

- *raw-loader* - 以字符串形式返回文件内容。
- *url-loader* - 如果文件大小低于某个阈值，则返回base64编码数据URL，否则只返回文件。
- *css-loader* - 在CSS文件中将`@import`和`url`引用解析为模块。
- *style-loader* - 在`<head>`标签中插入带有捆绑CSS的样式标签。