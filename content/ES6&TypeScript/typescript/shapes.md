# Shapes

下面的TypeScript是JavaScript，下面的JavaScript通常是一个JIT（即时编译器）。 给定JavaScript的底层语义，类型通常由“形状”推理。 这些底层形状像TypeScript的接口一样工作，事实上TypeScript比较类和类接口之类的自定义类型。

考虑前面示例的扩展：

```typescript
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
尽管`Action`与`NotAnAction`具有不同的标识符， 但是`tsc`允许我们将`NotAnAction`的实例分配给具有`Action`类型的实例。这是因为TypeScript只真的关心对象具有相同的形状。换句话说，如果两个对象具有相同的属性，具有相同的类型，那么这两个对象被认为是相同类型。