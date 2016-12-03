#TypeScript类

TypeScript还将类视为自己的类型：
```ts
class Foo { foo: number; }
class Bar { bar: string; }

class Baz { 
  constructor(foo: Foo, bar: Bar) { }
}

let baz = new Baz(new Foo(), new Bar()); // valid
baz = new Baz(new Bar(), new Foo());     // tsc errors
```
像函数参数一样，类有时具有可选成员。 同样的`?:`语法可以用于类定义：
```ts
class Person {
  name: string;
  nickName?: string;
}
```
在上面的示例中，Person的实例保证有一个名称，并且可以可选地具有nickName。