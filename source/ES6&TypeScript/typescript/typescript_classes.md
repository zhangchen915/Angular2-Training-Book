#TypeScript类

TypeScript还将类视为自己的类型：
```typescript
class Foo { foo: number; }
class Bar { bar: string; }

class Baz { 
  constructor(foo: Foo, bar: Bar) { }
}

let baz = new Baz(new Foo(), new Bar()); // valid
baz = new Baz(new Bar(), new Foo());     // tsc errors
```
像函数参数一样，类有时具有可选成员。 同样的`?:`语法可以用于`class`定义：
```typescript
class Person {
  name: string;
  nickName?: string;
}
```
在上面的示例中，`Person`的实例保证有一个`name`，并且可以可选地具有`nickName`。

#### 修饰符

在类中修饰符分为三种：`public` 公有、`private` 私有和`protected`受保护。


- public：类的每个成员默认为`public`，可以自由被访问。

- private：类成员被标记为`private`时就不能在类的外部访问。

- protected：`protected`和`private`行为很相似，但`protected`成员可以在派生类中访问。


#### 继承与多态

封装，继承和多态是面型对象的三大特性。

TypeScript的继承与ES6基本一致。

多态是同一个行为具有多个不同表现形式或形态的能力。比如在各种程序中对键盘的同一个按键事件响应都不相同，这就是多态性。

实现多态的方法有：重写、接口、抽象类和抽象方法。