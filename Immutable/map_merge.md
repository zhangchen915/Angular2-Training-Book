#Map.merge
有时我们想更新多个属性。 我们可以使用merge方法做到这一点。
```ts
let baseButton = Immutable.Map<string, any>({
  text: 'Click me!',
  state: 'inactive',
  width: 200,
  height: 30
});

let submitButton = baseButton.merge({
  text: 'Submit',
  state: 'active'
});

console.log(submitButton);
// writes { text: 'Submit', state: 'active', width: 200, height: 30 }
```
