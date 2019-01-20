# 上下文传播

上下文传播允许将数据附加到 zone。 然后，当数据在异步调用中传播时，数据与 zone 一起传输。

```js
let rootZone = Zone.current;
// Fork rootZone和attach属性
let zoneA = rootZone.fork({name: 'zoneA', properties: {a: 1, b:1}});
// Fork zoneA和attach属性，其中一些覆盖zoneA。
let zoneB = zoneA.fork({name: 'zoneA'[d], properties: {a: 2}});
// 查询zoneA返回预期值
expect(zoneA.get('a')).toEqual(1);
expect(zoneA.get('b')).toEqual(1);
// 查询zoneB返回zoneB定义'a'为2的预期值。
expect(zoneB.get('a')).toEqual(2);
// 查询zoneB的'b'返回zoneA的'b'，因为zoneB没有'b'的值。
expect(zoneB.get('b')).toEqual(1);
```

**关键点：**

- 附加到该区域的数据shallow-immutable。 （一旦 zone 被 fork，它的属性不能被改变。）
- 子 zone 继承父 zone 属性。（在子 zone 中运行的代码应该与在父 zone 中运行的代码相同，前提是子 zone 没有显式覆盖代码所依赖的值。）
- 因为 zone 流通过异步操作，所以相应的属性与 zone 流允许以某种形式进行通信。