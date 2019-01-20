# ES6 模块

ES6引入 *模块* 支持。 ES6中的模块是单个文件，它允许代码和数据隔离，它有助于在逻辑上组织和聚合代码。 在其他语言中，它被称为包或库。

模块内的所有代码和数据都有文件作用域，这意味着它们不能被模块外部的代码访问。 要在模块外部共享代码或数据，需要使用**export**关键字导出。

```typescript
// File: circle.js

export const pi = 3.141592;

export const circumference = diameter => diameter * pi;
```

上面的代码使用了箭头函数`circumference`。

## 模块系统

在后端（服务器端）使用模块是相对简单的，你只需使用**import**关键字。 然而，Web浏览器没有模块或导入的概念，他们只知道如何加载JavaScript代码。 我们需要一种方法来引入一个javascript模块，以便从其他JavaScript代码开始使用它。 这就是模块加载器要解决的问题。

我们不会讲解各种模块系统，但值得了解的是，有各种模块加载器可用。 流行的选择有：

- RequireJS
- SystemJS
- Webpack

## 从浏览器加载模块

下面我们使用SystemJS来加载模块。 该脚本首先加载SystemJS库的代码，然后调用**System.import**的函数用于导入（加载）应用程序模块。

加载ES6模块有点棘手。 在符合ES6的浏览器中，使用System关键字以异步方式加载模块。 为了使我们的代码与当前的浏览器一起使用，我们将使用SystemJS库作为polyfill：

```html
  <script src="/node_module/systemjs/dist/system.js"></script>
  <script>
    var promise = System.import('app')
      .then(function() {
        console.log('Loaded!');
      })
      .then(null, function(error) {
        console.error('Failed to load:', error);
      });
  </script>
```