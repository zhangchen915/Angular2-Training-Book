#TypeScript类

打字稿也把class上课当作自己的类型：
class Foo { foo: number; }
class Bar { bar: string; }

class Baz { 
  constructor(foo: Foo, bar: Bar) { }
}

let baz = new Baz(new Foo(), new Bar()); // valid
baz = new Baz(new Bar(), new Foo());     // tsc errors
像函数的参数，classES有时会具有可选的成员。同样的 ?:语法可以在使用class的定义：
class Person {
  name: string;
  nickName?: string;
}
在上面的例子中，一个实例Person是保证有一个name，并且可能任选地具有一个nickName