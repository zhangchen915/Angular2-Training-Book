#接口

有时类比程序员想要的“更多”。类最终以转换的ES6类或转换的ES5构造函数的形式创建代码。

此外，JavaScript是一门集打字稿，并在JavaScript函数是“一流”（它们可以被分配到变量，通过周围），这样怎么能功能打字稿描述？
TypeScript的接口解决了这两个问题。接口是对事物的抽象描述，可以用来表示任何非原始JavaScript对象。他们生产的无代码（ES6或ES5），只存在来形容类型tsc。
这里是描述函数的接口的示例：
```
interface Callback {
  (error: Error, data: any): void;
}

function callServer(callback: Callback) {
  callback(null, 'hi');
}
callServer((error, data) => console.log(data)); // 'hi'
callServer('hi');                               // tsc error
```
有时JavaScript函数是“重载的” - 也就是说，它们可以有不同的调用签名。接口可以用来指定。（类中的方法也可以重载）：
```
interface PrintOutput {
  (message: string): void;    // common case
  (message: string[]): void;  // less common case
}

let printOut: PrintOutput = (message) => {
  if (Array.isArray(message)) {
    console.log(message.join(', '));
  } else {
    console.log(message);
  }
}

printOut('hello');       // 'hello'
printOut(['hi', 'bye']); // 'hi, bye'
```
这是一个描述对象字面量的接口的示例：
```
interface Action {
  type: string;
}

let a: Action = {
    type: 'literal' 
}
```