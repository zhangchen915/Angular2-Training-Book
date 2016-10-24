#Angular2中的组件

组件
图：组件
任何角2应用程序的核心理念是组件。实际上，整个应用程序可以被建模为这些组件的树。
这是Angular 2团队定义组件的方式：
组件控制一个屏幕空间的补丁，我们可以调用视图，并为应用程序声明可重用的UI构建块。
基本上，组件是最终用户可见的任何东西，并且可以在应用程序中重复使用多次。
在Angular1.x中，我们有路由器视图和指令，其工作类似于组件。指令组件的想法变得相当受欢迎。它们通过使用创建的directive具有控制器，而在靠controllerAs和bindToController属性。例如：
```js
angular.module('ngcourse')
  .directive('ngcHelloComponent', () => ({
      restrict: 'E',
      scope: { name: '=' },
      template: '<span>Hello, {{ ctrl.name }}.</span>',
      controller: MyComponentCtrl,
      controllerAs: 'ctrl',
      bindToController: true
    })
  );
```
事实上，这个概念变得如此受欢迎，在Angular 1.5中的`.component`方法是作为语法糖。
```js
angular.module('ngcourse')
  .component('ngcHelloComponent', {
    bindings: { name: '=' },
    template: '<span>Hello, {{ $ctrl.name }}.</span>',
    controller: MyComponentCtrl
  });
```