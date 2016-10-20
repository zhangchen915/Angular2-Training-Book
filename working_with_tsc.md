#使用`tsc`
到目前为止`tsc`已用于编译单个文件。通常，程序会有多个文件进行编译。值得庆幸的是`tsc`可以处理多个文件作为参数。

a.ts
```ts
export const A = (a) => console.log(a);
```
b.ts
```ts
export const B = (b) => console.log(b);
```
TypeScript1.8.2之前：
```
$ tsc ./a.ts ./b.ts
a.ts(1,1): error TS1148: Cannot compile modules unless the '--module' flag is provided.
```
嗯。 这个模块标志的处理是什么？ TypeScript有一个帮助菜单，让我们来看看：
```
$ tsc --help | grep module
 -m KIND, --module KIND             Specify module code generation: 'commonjs', 'amd', 'system', 'umd' or 'es2015'
 --moduleResolution                 Specifies module resolution strategy: 'node' (Node.js) or 'classic' (TypeScript pre-1.6).
```
（TypeScript比我们显示的更多帮助;为了简洁，我们通过grep过滤）。有两个引用“module”的帮助条目，--module是一个TypeScript所抱怨的。 该描述解释了TypeScript支持许多不同的模块方案。 目前，commonjs是可取的。 这将产生与node.js的模块系统兼容的模块。
```
$ tsc -m commonjs ./a.ts ./b.ts
```
TypeScript1.8.2之后，tsc有一个默认规则--module选项：target ==='ES6'？ 'ES6'：'commonjs'（更多细节可以在这里找到），所以我们可以简单地运行：
```
$ tsc ./a.ts ./b.ts
```

tsc应该不产生输出。 在许多命令行传统中，没有任何输出实际上是成功的标志。 列出目录内容将确认我们的TypeScript文件已经编译完成。
```
$ ls
a.js    a.ts    b.js    b.ts
```

非常好 - 现在有两个JavaScript模块可供使用。
即使项目很小告诉`tsc`命令编译什么非常乏味和麻烦的。 幸运的是TypeScript有一个简化这个的方法。 `tsconfig.json`文件让程序员写下他们想要的所有编译器设置。 当运行`tsc`时，它会查找`tsconfig.json`文件并使用它们的规则来编译JavaScript。
对于Angular 2项目，有一些特定的设置需要在项目的`tsconfig.json`中配置。
```json
{
 "compilerOptions": {
    "module": "commonjs",
    "target": "es5",
    "emitDecoratorMetadata": true,
    "experimentalDecorators": true,
    "noImplicitAny": false,
    "removeComments": false,
    "sourceMap": true
  },
  "exclude": [
    "node_modules",
    "dist/"
  ]
}
```