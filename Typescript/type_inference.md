#类型推断

关于TypeScript类型的一个常见误解是代码需要在每个可能的机会上显式地描述类型。幸运的是这不是这样。TypeScript有一个丰富的类型推理系统，将为程序员“填空”。考虑以下：
type-in​​ference-found-error.ts
```
let numbers = [2, 3, 5, 7, 11];
numbers = ['this will generate a type error'];
```
```
tsc ./type-inference-finds-error.ts 
type-inference-finds-error.ts(2,1): error TS2322: Type 'string[]' is not assignable to type 'number[]'.
Type 'string' is not assignable to type 'number'.
 ```
代码不包含额外的类型信息。事实上，它是有效的ES6。
如果var已经使用，这将是有效的ES5。然而TypeScript仍然能够确定类型信息。
类型推理也可以通过上下文工作，这是回调方便。考虑以下：

type-inference-finds-error-2.ts
```
interface FakeEvent {
  type: string;
}

interface FakeEventHandler {
  (e: FakeEvent): void; 
}

class FakeWindow {
  onMouseDown: FakeEventHandler
}
const fakeWindow = new FakeWindow();

fakeWindow.onMouseDown = (a: number) => {
  // this will fail
};
tsc ./type-inference-finds-error-2.ts 
type-inference-finds-error-2.ts(14,1): error TS2322: Type '(a: number) => void' is not assignable to type 'FakeEventHandler'.
  Types of parameters 'a' and 'e' are incompatible.
    Type 'number' is not assignable to type 'FakeEvent'.
      Property 'type' is missing in type 'Number'.
```
在此示例中，上下文不明显，因为接口已被明确定义。在浏览器环境中与真正的window对象，这将是一个方便的功能，特别是类型完成Event 对象。