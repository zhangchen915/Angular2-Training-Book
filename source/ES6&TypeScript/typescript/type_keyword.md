# 类型关键字

`type`关键字定义类型的别名。

```typescript
type str = string;
let cheese: str = 'gorgonzola';
let cake: str = 10; // Type 'number' is not assignable to type 'string'
```

乍一看，这似乎并不是非常有用（即使是错误提及原始类型），但随着类型注释变得更加复杂，`type` 关键字的好处变得明显。

### 联合类型

联合类型允许类型注释指定属性应该是一组类型之一（两者任一）。

```typescript
function admitAge (age: number|string): string {
  return `I am ${age}, alright?!`;
}

admitAge(30); // 'I am 30, alright?!'
admitAge('Forty'); // 'I am Forty, alright?!'
```

`type`关键字简化了注释和重用联合类型。

```typescript
type Age = number | string;

function admitAge (age: Age): string {
  return `I am ${age}, alright?!`;
}

let myAge: Age = 50;
let yourAge: Age = 'One Hundred';
admitAge(yourAge); // 'I am One Hundred, alright?!'
```

联合类型的字符串文字类型是一个非常有用的模式，创建基本上是带有字符串值的枚举。

```typescript
type PartyZone = "pizza hut" | "waterpark" | "bowling alley" | "abandoned warehouse";

function goToParty (place: PartyZone): string {
  return `lets go to the ${place}`;
}

goToParty("pizza hut");
goToParty("chuck e. cheese"); // Argument of type `"chuck e. cheese"' is not assignable to parameter of type 'PartyZone'
```

### 交叉类型

交叉类型是两种或更多种类型的组合。 适用于需要实现多个接口的对象和参数。

```typescript
interface Kicker {
  kick(speed: number): number;
}

interface Puncher {
  punch(power: number): number;
}
// assign intersection type definition to alias KickPuncher
type KickPuncher = Kicker & Puncher;

function attack (warrior: KickPuncher) {
  warrior.kick(102);
  warrior.punch(412);
  warrior.judoChop(); // Property 'judoChop' does not exist on type 'KickPuncher'
}
```

### 函数类型定义

函数类型注释可以比内置函数类型的typescript更加具体。 `Function`类型定义允许您将函数签名附加到它自己的类型。

```typescript
type MaybeError = Error | null;
type Callback = (err: MaybeError, response: Object) => void;

function sendRequest (cb: Callback): void {
  if (cb) {
    cb(null, {});
  }
}
```

语法与ES6 fat-arrow函数类似。`([params]) => [return type]`.

前面为了说明类型关键字而提高了代码片段的可读性，这里是定义为inline的函数类型。

```typescript
function sendRequest (cb: (err: Error|null, response: Object) => void): void {
  if (cb) {
    cb(null, {});
  }
}
```