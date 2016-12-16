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

```
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

The preLoaders array works just like the loaders array only it is a separate task chain that is executed before the loaders task chain.

### Non JavaScript Assets

Webpack also allows us to load non JavaScript assets such as: CSS, SVG, font files, etc. In order to attach these assets to our bundle we must require/import them within our app modules. For example:

```
import './styles/style.css';

// or

const STYLES = require('./styles/style.css');

```

### Other Commonly Used Loaders

TypeScript isn't core JavaScript so webpack needs a bit of extra help to parse the `.ts` files. It does this through the use of *loaders*. Loaders are a way of configuring how webpack transforms the outputs of specific files in our bundles. Our `ts-loader` package is handling this transformation for TypeScript files.

## Inline

Loaders can be configured – inline – when requiring/importing a module:

```
const app = require('ts!./src/index.ts');

```

The loader is specified by using the `!` character to separate the module reference and the loader that it will be run through. More than one loader can be used and those are separated with `!` in the same way. Loaders are executed right to left.

```
const app = require('ts!tslint!./src/index.ts');

```

> Although the packages are named `ts-loader`, `tslint-loader`, `style-loader`, we don't need to include the `-loader` part in our config.

Be careful when configuring loaders this way – it couples implementation details of different stages of your application together so it might not be the right choice in a lot of cases.

## Webpack Config

The preferred method is to configure loaders through the `webpack.config.js` file. For example, the TypeScript loader task will look something like this:

```
{
  test: /\.ts$/,
  loader: 'ts-loader',
  exclude: /node_modules/
}

```

This runs the typescript compiler which respects our configuration settings as specified above. We want to be able to handle other files and not just TypeScript files, so we need to specify a list of loaders. This is done by creating an array of tasks.

Tasks specified in this array are chained. If a file matches multiple conditions, it will be processed using each task in order.

```
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

Each task has a few configuration options:

- *test* - The file path must match this condition to be handled. This is commonly used to test file extensions eg. `/\.ts$/`.
- *loader* - The loaders that will be used to transform the input. This follows the syntax specified above.
- *exclude* - The file path must not match this condition to be handled. This is commonly used to exclude file folders, e.g. `/node_modules/`.
- *include* - The file path must match this condition to be handled. This is commonly used to include file folders. eg. `path.resolve(__dirname, 'app/src')`.

### Pre-Loaders

The preLoaders array works just like the loaders array only it is a separate task chain that is executed before the loaders task chain.

### Non JavaScript Assets

Webpack also allows us to load non JavaScript assets such as: CSS, SVG, font files, etc. In order to attach these assets to our bundle we must require/import them within our app modules. For example:

```
import './styles/style.css';

// or

const STYLES = require('./styles/style.css');

```

### Other Commonly Used Loaders

- *raw-loader* - returns the file content as a string.
- *url-loader* - returns a base64 encoded data URL if the file size is under a certain threshold, otherwise it just returns the file.
- *css-loader* - resolves `@import` and `url` references in CSS files as modules.
- *style-loader* - injects a style tag with the bundled CSS in the `` tag.Loaders


- *raw-loader* - returns the file content as a string.
- *url-loader* - returns a base64 encoded data URL if the file size is under a certain threshold, otherwise it just returns the file.
- *css-loader* - resolves `@import` and `url` references in CSS files as modules.
- *style-loader* - injects a style tag with the bundled CSS in the `` tag.