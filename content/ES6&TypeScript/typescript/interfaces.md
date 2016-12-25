# 接口

一个*接口*是一个TypeScript工件，它不是ECMAScript的一部分。 接口是一种用于相对于参数及其类型定义函数上的契约的方式。 除了函数，接口也可以与类一起使用以定义自定义类型

接口是一个抽象类型，它不像类一样包含任何代码。 它仅定义API的“签名”或形状。 在代码转换期间，`interface` 不会生成任何代码，它仅在开发期间被Typescript用于类型检查。

下面是描述函数API的接口的示例：

```typescript
interface Callback {
  (error: Error, data: any): void;
}

function callServer(callback: Callback) {
  callback(null, 'hi');
}

callServer((error, data) => console.log(data));  // 'hi'
callServer('hi');                                // tsc error
```

有时JavaScript函数可以接受多个类型以及不同的参数，也就是说，它们可以有不同的调用签名。接口可以用来指定。

```typescript
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

下面是一个描述对象字面量的接口的示例：

```typescript
interface Action {
  type: string;
}

let a: Action = {
    type: 'literal'
}
```