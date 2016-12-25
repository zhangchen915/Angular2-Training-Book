# 参数装饰器

```javascript
function logPosition(target: any, propertyKey: string, parameterIndex: number) {
  console.log(parameterIndex);
}

class Cow {
  say(b: string, @logPosition c: boolean) {
    console.log(b); 
  }
}

new Cow().say('hello', false); // outputs 1 (newline) hello
```

上面演示了装饰方法的参数。熟悉Angular 2的读者现在可以想象Angular 2如何实现他们的`@Inject()`系统。