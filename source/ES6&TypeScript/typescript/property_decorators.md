# 属性装饰器

属性装饰器使用类的属性。

```typescript
function Override(label: string) {
  return function (target: any, key: string) {
    Object.defineProperty(target, key, { 
      configurable: false,
      get: () => label
    });
  }
}

class Test {
  @Override('test')      // invokes Override, which returns the decorator
  name: string = 'pat';
}

let t = new Test();
console.log(t.name);  // 'test'
```

以上示例必须使用`--experimentalDecorators`和`--emitDecoratorMetadata`标志编译。

在这种情况下，装饰属性由传递给装饰器的标签替换。 重要的是注意属性值不能由装饰器直接操纵; 而是使用访问器。

这里是一个使用普通装饰器的经典属性示例。

```typescript
function ReadOnly(target: any, key: string) {
  Object.defineProperty(target, key, { writable: false });
}

class Test {
  @ReadOnly             // notice there are no `()`
  name: string;
}

const t = new Test();
t.name = 'jan';         
console.log(t.name); // 'undefined'
```

在这种情况下，name属性不可写，并且保持未定义。