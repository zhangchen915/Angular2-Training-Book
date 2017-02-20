# Zone 传播

第一章介绍如何创建 Zones，并通过异步函数传播

## Entering Zones, Forking and Stack Frames

首先要了解的是如何创建 zone（forked），如何通过系统传播，以及如何形象的理解 zones 和堆栈帧。

```js
// RootZone 环境的所以它难以与无 zone 区分。
let rootZone = Zone.current;
// We create a new zone by forking an existing zone.
let zoneA = rootZone.fork({name: 'zoneA'});
// Each zone has a name for debugging
expect(rootZone.name).toEqual('<root>');
expect(zoneA.name).toEqual('zoneA');
// Child zone knows about its parent zone. (one way reference)
expect(zoneA.parent).toBe(rootZone);
function main() {
    // zones can be entered/exited using the `run` method only.
    zoneA.run(function fnOuter() {
        // inside the `run` method the Zone.current has been updated
        expect(Zone.current).toBe(zoneA);
        // Mental model: Each stack frame is associated with a zone
        expect(Error.captureStackTrace()).toEqual(outerLocation)
        // Zones are nested in the same way that stack frames are nested.
        rootZone.run(function fnInner() {
            // There is no reason why a nested stack frame must be
            // a child of parent stack frame zone.
            // This is how one can "escape" a zone.
            expect(Zone.current).toBe(rootZone);
            expect(Error.captureStackTrace()).toEqual(innerLocation)
        });
    });
}
main();
```

**关键点：**

- Assigning to Zone.current is a runtime error. The only way to change Zone.current is through Zone.prototype.run().
- A given stack frame has exactly one zone associated with it. The stack frame above or below must have the same zone, unless that stack frame is Zone.prototype.run().
- Child zone has a reference to parent zone (but parent does not have reference to child) having parent relationship only allows the zones to be garbage collected by simply releasing a reference to a zone.

### Stack Frames

It is important to understand that a given stack frame can only be associated with one zone. (i.e. it is not possible for first half of a function to run in a different zone than later half of the function. It is possible that the same function will have different zone on different invocations). Zone can only be entered or left by entering or exiting Zone.prototype.run(). Zones updates stack traces to show zones for better visibility. Below are two stack snapshots from the above example which show the associated zone for each stack frame.

重要的是要理解，给定的堆栈帧只能与一个区域相关联。 （即，函数的前半部分不可能在与函数的后半部分不同的区域中运行。相同的函数可能在不同的调用上具有不同的区域）。 只能通过输入或退出Zone.prototype.run（）来输入或留下区域。 区域更新堆栈跟踪以显示区域，以提高可见性。 下面是来自上面示例的两个堆栈快照，其示出了每个堆栈帧的关联区域。

```
outerLocation:

  at fnOuter()[ZoneA];
  at Zone.prototype.run()[<root> -> zoneA]
  at main()[<root>]
  at <anonymous>()[<root>]
```

```
innerLocation:

  at fnInner()[<root>];
  at Zone.prototype.run()[zoneA -> <root>]
  at fnOuter()[zoneA];
  at Zone.prototype.run()[<root> -> zoneA]
  at main()[<root>]
  at <anonymous>()[<root>]
```

