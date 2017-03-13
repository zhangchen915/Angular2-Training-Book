#类型⚡️

TypeScript 提供了以下基本类型：

- 布尔（boolean）
- 数字（number）
- 字符串（string）
- 数组（array）
- 元组（tuple）
- 枚举（enum）
- 任意值（any）
- null 和 undefined
- void
- never

其中元组、枚举、任意值、void和never类型是 TypeScript 特有的。

#### 元组类型

元组类型用于表示已知元素类型和数量的数组，各类型不必相同。

```typescript
let x: [string, number];
x= ['angular', 25];
x= [25, 'angular']; //报错
```

#### 枚举类型

枚举是一个可被命名的整型常数的集合，枚举类型为集合成员赋予有意义的名称，增强可读性。

```typescript
enum week{ Mon, Tues, Wed, Thurs, Fri, Sat, Sun };
let Wed: week = Color.Wed;
console.log(Wed) // 2
```

枚举值默认从 0 开始，往后逐个加 1（递增）；也就是说，week 中的 Mon、Tues ...... Sun 对应的值分别为 0、1 ...... 6。也可以给每个名字都指定一个值，但大多数我们给第一个名字指定值就可以了`enum week{ Mon = 1, Tues, Wed, Thurs, Fri, Sat, Sun };`

#### 任意值类型和void类型

任意值是TypeScript针对类型不明确的变量使用的一种数据类型。

void表示没有任何类型。例如一个函数没有返回值时，那他返回的的类型是void。

#### never类型

 never 是所有类型的子类型，代表从来不会出现的值的类型。

```typescript
// 返回 never 的函数必须有无法被执行到的终止点
function error(message: string): never {
    throw new Error(message);
}

// 推断的返回值是 never
function fail() {
    return error("一些东西失败了");
}

// 返回 never 的函数必须有无法被执行到的终止点
function infiniteLoop(): never {
    while (true) {
    }
}
```



精明的读者可能会想知道当TypeScript程序员需要调用没有类型信息的JavaScript模块接口时会发生什么。 TypeScript将标记为 `*.d.ts` 的文件视为定义文件。 这些文件旨在使用TypeScript来描述由JavaScript库提供的接口。

在TypeScript 2.0中，用户可以直接从`@types`通过`npm`获取类型文件（例如，`npm install --save @types/lodash`将安装`lodash`类型文件）。