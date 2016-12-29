# 性能和瞬态变化

## 性能

不可变的数据结构通常由于分配新的存储器和复制数据的成本而具有性能损失。考虑这两个例子，一个使用可变数组，一个使用Immutable.js集合。

**可变**

```javascript
const list = [];
let val = "";

Immutable.Range(0, 1000000)
  .forEach(function() {
    val += "concatenation";
    list.push(val);
  });
```

**不可变**

```javascript
const init = {
  list: Immutable.List(),
  val: ""
};

const list = Immutable.Range(0, 1000000)
  .reduce(function(reduced) {
    var next = reduced.val + "concatenation";

    return {
      list: reduced.list.push(next),
      val: next
    };
  }, init).list
```

这里完全不可变的代码运行的速度比可变代码慢大约90％！ 虽然不可变数据可以使代码更容易推理，但肯定有想相应的选择成本。 正如我们在这里可以看到的迭代concat，这可以对可用性有重大影响。 幸运的是，Immutable.js提供了一些可以降低性能开销的功能。

## 持久数据结构和瞬态变化

不可变数据结构有时也被称为持久数据结构，因为它们的值在其生存期内持续。 Immutable.js提供了临时更改的选项：不可变的数据结构可以在本地执行可变更改，同时返回不可变结果的操作。 这是解决我们之前遇到的性能问题的一种方法。 让我们重温性能示例中描述的不可变情况，但是这次使用瞬态数据结构：

```javascript
import * as Immutable from 'immutable';

let list = list.withMutations(mutableList => {
  let val = "";

  return Immutable.Range(0, 1000000)
    .forEach(() => {
      val += "concatenation";
      mutableList.push(val);
  });
});

console.log(list.size); // writes 1000000
list.push('');
console.log(list.size); // writes 1000000
```

这个瞬态列表生成器仍然比我们完全可变的实现慢得多，但比我们的完全不可变版本快得多。