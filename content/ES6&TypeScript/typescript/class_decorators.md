# 类装饰器

```javascript
function log(prefix?: string) {
  return (target) => {
    // save a reference to the original constructor
    var original = target;

    // a utility function to generate instances of a class
    function construct(constructor, args) {
      var c: any = function () {
        return constructor.apply(this, args);
      }
      c.prototype = constructor.prototype;
      return new c();
    }

    // the new constructor behavior
    var f: any = function (...args) {
      console.log(prefix + original.name);
      return construct(original, args);
    }

    // copy prototype so instanceof operator still works
    f.prototype = original.prototype;

    // return new constructor (will override original)
    return f;
  };
}

@log('hello')
class World {
}

const w = new World(); // outputs "helloWorld"
```

在示例中`log`使用`@`调用，并传递一个字符串作为参数，`@log()`返回一个匿名函数，是实际的装饰器。

装饰器函数接受一个类或构造函数（ES5）作为参数。 装饰器函数然后返回一个新的类构造函数，每当`World`被实例化时使用。

这个装饰器除了将其给定的参数和其目标的类名注销到控制台之外什么都不做。 	