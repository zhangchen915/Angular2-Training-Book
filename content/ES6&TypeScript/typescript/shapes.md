#形状

下面的TypeScript是JavaScript，下面的JavaScript通常是一个JIT（即时编译器）。给定JavaScript的底层语义，类型通常由“形状”推理。这些基本形状的工作就像打字稿的接口，实际上打字稿如何比较自定义类型像classES和interface秒。
考虑前面示例的扩展：
```ts
interface Action {
  type: string;
}

let a: Action = {
    type: 'literal' 
}

class NotAnAction {
  type: string;
  constructor() {
    this.type = 'Constructor function (class)';
  }
}

a = new NotAnAction(); // valid TypeScript!
```
尽管Action与NotAnAction具有不同的标识符， tsc让我们分配的一个实例NotAnAction，以a具有类型 Action。这是因为TypeScript只真的关心对象具有相同的形状。换句话说，如果两个对象具有相同的属性，具有相同的类型，那么这两个对象被认为是相同类型。