#接口

一个接口是一个TypeScript工件，它不是ECMAScript的一部分。 接口是一种相对于参数及其类型定义函数上的契约的方式。 除了函数，接口也可以与类一起使用以定义自定义类型

接口是一个抽象类型，它不像类一样包含任何代码。 它仅定义API的“签名”或形状。 在换代期间，接口不会生成任何代码，它仅在开发期间用于Typescript类型检查。

这里是描述函数的接口的示例：
```ts
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
```ts
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