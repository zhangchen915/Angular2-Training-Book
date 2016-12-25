# 装饰器

装饰器被提议用于未来版本的JavaScript，但是Angular 2团队真的想使用它们，并且它们已经被包括在TypeScript中。

装饰器是用前缀`@`符号调用的函数，后面紧跟一个类，参数，方法或属性。 装饰器函数提供关于类，参数或方法的信息，装饰器函数返回某处的位置，或以某种方式操纵其目标。 通常，装饰器返回的“东西”与传入的东西是相同的，但是它以某种方式被增强了。

装饰器在TypeScript中是相当新的，并且大多数使用案例展示了现有装饰器的使用。 然而，装饰器只是函数，并且通过几个例子更容易理解。

装饰器是函数，有四个东西（类，参数，方法和属性）可以装饰; 因此装饰器有四个不同的函数签名：

- class: `declare type ClassDecorator = (target: TFunction) => TFunction | void;`
- property: `declare type PropertyDecorator = (target: Object, propertyKey: string | symbol) => void;`
- method: `declare type MethodDecorator = (target: Object, propertyKey: string | symbol, descriptor: TypedPropertyDescriptor) => TypedPropertyDescriptor | void;`
- parameter: `declare type ParameterDecorator = (target: Object, propertyKey: string | symbol, parameterIndex: number) => void;`

已经使用Angular 2的读者会注意到这些签名看起来不像Angular 2特定装饰器，如`@Component()`使用的签名。

注意  `@Component`上的`()` ，这意味着`@Component`在JavaScript遇到`@Component()`时被调用。 反过来，这意味着必须有一个组件函数在某处返回一个匹配上面描述的装饰器签名的函数。 这是装饰工厂模式的一个例子。

如果装饰器仍然看起来很困惑，也许一些例子会帮你理清思路。