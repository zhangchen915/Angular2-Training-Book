#TypeScript入门
使用npm安装TypeScript转换器：
```
$ npm install -g typescript
```
然后使用`tsc`手动将TypeScript源文件编译为ES5：
```
$ tsc test.ts
$ node test.js
```
##关于ES6示例

我们早期的ES6类现在不会编译。 TypeScript比ES6更苛刻，它需要声明实例属性：
```ts
class Pizza {
  toppings: string[];
  constructor(toppings: string[]) {
    this.toppings = toppings;
  }
}
```
注意，现在我们已经声明`toppings`是一个字符串数组，TypeScript将强制这样做。 如果我们尝试为它分配一个数字，我们将在编译时收到一个错误。
如果你想让一个属性的值可以设置为任何类型，你只需声明其类型为“any”：
```ts
class Pizza {
  toppings: any;
  //...
}
```