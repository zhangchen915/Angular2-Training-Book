#创建组建
CLI可以通过`generate`命令支持Angular 2组件。 要创建新的组件运行：
`ng generate component [component-name]`
执行命令会在项目的`src / app`路径中创建一个文件夹`[component-name]`，如果它是项目的子文件夹，则会执行该命令的当前路径。 该文件夹具有以下内容：

* `[component-name].component.ts`组件类文件
* `[component-name].component.css`用于样式化组件
* `[component-name].component.html` html组建
* `[component-name].component.spec.ts`测试组件
* `index.ts`，用于导出组件