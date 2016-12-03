#ES6模块
ES6还引入了一个模块的概念，它类似于其他语言。 定义ES6模块是很容易的：假定每个文件定义一个模块，并使用export关键字指定它的导出值。
加载ES6模块有点棘手。 在符合ES6的浏览器中，使用System关键字以异步方式加载模块。 为了使我们的代码与当前的浏览器一起工作，我们将使用SystemJS库作为polyfill：
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
 
 ###译注
这个特性目前没有在所有浏览器中原生实现。