# Karma 配置

Karma是我们测试工作流程的基础。 它集合了我们的其他测试工具来定义我们想要使用的框架，测试环境，我们想要执行的具体操作等。为了做到这一点，Karma依赖于一个以默认`karma.conf.js`命名的配置文件。
您可以通过`karma init`命令生成新的配置文件，这将指导您完成几个基本问题，以便运行最基本的设置。

## 概述

通过导出一个接受Karma将要使用的配置对象的函数，可以将配置文件放在一起。 修改此对象的某些属性将告诉Karma我们想要做什么。 让我们来看一下在这个配置文件中使用的一些关键属性：

```javascript
module.exports = (config) => {
  const coverage = config.singleRun ? ['coverage'] : [];

  config.set({
    frameworks: [...],
    plugins: [ ... ],
    files: [ ... ],
    preprocessors: { ... },

    webpack: { ... },

    reporters: [ ... ],
    coverageReporter: { ... },

    port: 9999,
    browsers: ['Chrome'], // 或者: 'PhantomJS'
    colors: true,
    logLevel: config.LOG_INFO,
    autoWatch: true,
    captureTimeout: 6000,
  });
};
```

### 框架

```
frameworks: [
  'jasmine',
],
```

`frameworks`是我们要使用的测试框架的列表。这些框架必须通过NPM作为依赖项安装在我们的项目中，或/和作为Karma插件。

### 插件

```
plugins: [
  'karma-jasmine',
  'karma-webpack',
  'karma-coverage',
  'karma-remap-istanbul',
  'karma-chrome-launcher',
],

```

将karma与测试框架（如Jasmine）或构建系统（如Webpack）集成的插件。

### files

```
files: [
  './src/tests.entry.ts',
  {
    pattern: '**/*.map',
    served: true,
    included: false,
    watched: true,
  },
],

```

`files` 是要加载到浏览器/测试环境中的文件的列表。 这些文件会按顺序加载，所以顺序很重要。 文件列表还可以采用glob模式的形式，因为为每个创建的新测试脚本手动添加新文件变得相当繁琐。
在angular2-redux-starter karma.conf.js中，我们将我们希望包含的测试文件放在一个单独的文件 - *src / tests.entry.ts*中，它包含一个require调用，使用regex模式导入文件**. spec.ts**文件扩展名。 随着项目越来越大，要包括的文件数量越来越复杂，这是一个很好的做法是将文件导入到一个单独的文件中 - 这使*karma.conf.js*文件更清晰，更易读。 这里是我们的*src / tests.entry.ts*看起来像：

```
let testContext = (<{ context?: Function }>require).context('./', true, /\.test\.ts/);
testContext.keys().forEach(testContext);

```

### preprocessors

```
preprocessors: {
  './src/tests.entry.ts': [
    'webpack',
    'sourcemap',
  ],
  './src/**/!(*.test|tests.*).(ts|js)': [
    'sourcemap',
  ],
}

```

`preprocessors` allow for some operation to be performed on the contents of a unit testing file before it is executed. These operations are carried out through the use of Karma plugins and are often used for transpiling operations. Since we are writing unit tests in TypeScript, *.ts* files must be transpiled into plain Javascript in order to run in a browser-based environment.

In angular2-redux-starter this process is done with webpack, so we explicitly invoke the `webpack`processor on all of our testing files (those ending with *.spec.ts*). We also load any source map files originating from transpilation through the `sourcemap` processor.

### webpack

```js
 webpack: {
  plugins,
  entry: './src/tests.entry.ts',
  devtool: 'inline-source-map',
  resolve: {
    extensions: ['.webpack.js', '.web.js', '.ts', '.js'],
  },
  module: {
    rules:
      combinedLoaders().concat(
        config.singleRun
          ? [ loaders.istanbulInstrumenter ]
          : [ ]),
  },
  stats: { colors: true, reasons: true },
},
webpackServer: {
  noInfo: true, // prevent console spamming when running in Karma!
}
```

If the project uses webpack, then the property `webpack` in the Karma configuration object is where we can configure webpack with Karma. In the angular2-redux-starter, plugins and loaders are exported from their own files to be imported by both the webpack config and the karma config, making the configuration object smaller.

Using webpack, we can configure how to bundle our unit tests; that is, whether to pack all tests into a single bundle, or each unit test in its own bundle, etc. Regardless, unit tests should not be bundled with the rest of the applications code (especially in production!). In angular2-redux-starter we have opted to bundle all unit tests together.

### coverageReporters 和 reporters

```
reporters: ['spec']
  .concat(coverage)
  .concat(coverage.length > 0 ? ['karma-remap-istanbul'] : []),

remapIstanbulReporter: {
  src: 'coverage/chrome/coverage-final.json',
  reports: {
    html: 'coverage',
  },
},

coverageReporter: {
  reporters: [
    { type: 'json' },
  ],
  dir: './coverage/',
  subdir: (browser) => {
    return browser.toLowerCase().split(/[ /-]/)[0]; // returns 'chrome'
  },
},
```

`coverageReporter`用于配置我们的代码覆盖率工具（我们的工具链使用伊斯坦布尔）的结果输出。 这里我们指定以JSON和HTML格式输出结果。 报告将显示在*coverage/*文件夹中。

`reporters` 是在测试循环中使用的 reporters 的列表。 `reporters` 可以被认为是用于报告核心单元测试之外的测试例程的某些方面的模块化工具。 代码覆盖是`reporters` 的一个例子 - 我们希望它报告我们的代码有多少被测试。 [有更多的 reporters 可用于Karma](https://www.npmjs.com/browse/keyword/karma-reporter)，可以帮助制定您的测试工作流。

### 环境配置

```
port: 9999,
browsers: ['Chrome'], // Alternatively: 'PhantomJS'
colors: true,
logLevel: config.LOG_INFO,
autoWatch: true,
captureTimeout: 6000,
```

`port`, `browsers` 和`singleRun` 配置我们的单元测试将运行的环境。 `browsers`属性指定了我们希望Karma启动和捕获输出的浏览器。 我们可以使用Chrome，Firefox，Safari，IE或Opera（需要为每个浏览器安装额外的Karma启动器）。 对于无浏览器的DOM实例，我们可以使用PhantomJS（如工具链部分中所述）。

我们还可以通过浏览到`http://localhost:port`手动捕获浏览器的输出，其中port是port属性中指定的数字（如果未指定，则默认值为9876）。 属性`singleRun`控制Karma如何执行，如果设置为`true`，Karma将启动配置的浏览器，运行测试，然后退出`0`或`1` ，这取决于是否所有测试通过。

## 完成配置

定制所有这些属性的最终结果是在[angular-redux-starter中的karma.conf.js文件](https://github.com/rangle/angular2-redux-example/blob/master/karma.conf.js)。

## 其他资源

这只是angular2-redux-starter项目使用的karma.conf.js中的核心属性的示例。 还有更多的属性可以用于扩展和配置Karma的功能 - [ 看看完整的官方API文档](http://karma-runner.github.io/0.13/config/configuration-file.html)。