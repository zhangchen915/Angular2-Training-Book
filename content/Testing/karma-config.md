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

```
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

### coverageReporters and reporters

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

`coverageReporter` is used to configure the output of results of our code coverage tool (our toolchain uses Istanbul). Here we have specified to output the results in JSON and HTML. Reports will appear in the *coverage/* folder.

`reporters` is a list of reporters to use in the test cycle. Reporters can be thought of as modular tools used to report on some aspect of the testing routine outside of the core unit tests. Code coverage is an example of a reporter - we want it to report on how much of our code is being tested. [There are many more reporters available for Karma](https://www.npmjs.com/browse/keyword/karma-reporter) that can aid in crafting your testing workflow.

### Environment configuration

```
port: 9999,
browsers: ['Chrome'], // Alternatively: 'PhantomJS'
colors: true,
logLevel: config.LOG_INFO,
autoWatch: true,
captureTimeout: 6000,

```

`port`, `browsers` and `singleRun` configure the environment our unit tests will run under. The `browsers`property specifies which browser we want Karma to launch and capture output from. We can use Chrome, Firefox, Safari, IE or Opera (requires additional Karma launcher to be installed for each respective browser). For a browser-less DOM instance we can use PhantomJS (as outlined in the toolchain section).

We can also manually capture output from a browser by navigating to `http://localhost:port`, where port is the number specified in the `port` property (the default value is 9876 if not specified). The property `singleRun` controls how Karma executes, if set to `true`, Karma will start, launch configured browsers, run tests and then exit with a code of either `0` or `1` depending on whether or not all tests passed.

## Completed Configuration

The net result of customizing all of these proprties is the [*karma.conf.js* file in angular-redux-starter](https://github.com/rangle/angular2-redux-example/blob/master/karma.conf.js).

## Additional Resources

This is just a sample of the core properties in *karma.conf.js* being used by angular2-redux-starter project. There are many more properties that can be used to extend and configure the functionality of Karma - [take a look at the official documentation for the full API breakdown](http://karma-runner.github.io/0.13/config/configuration-file.html).