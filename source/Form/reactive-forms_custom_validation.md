# 自定义验证响应式表单

内置验证器非常有用，能够引入你自己的验证方式。 Angular 2可以让你以很轻松地做到。

让我们假设使用相同的登录表单，但现在我们还要测试我们的密码在其中某处有感叹号。

*app/login-form.component.ts*

```typescript
function hasExclamationMark(input: FormControl) {
  const hasExclamation = input.value.indexOf('!') >= 0;
  return hasExclamation ? null : { needsExclamation: true };
}


// ...
this.password = new FormControl('', [
  Validators.required,
  hasExclamationMark
]);
```

一个简单的函数接受`FormControl`实例，并返回null如果一切都很好。 如果测试失败，它返回一个具有任意命名属性的对象。 属性名称将用于`.hasError()`测试。

*app/login-form.component.ts*

```html
<!-- ... -->
<div [hidden]="!password.hasError('needsExclamation')">
  Your password must have an exclamation mark!
</div>
<!-- ... -->
```

[View Example](https://plnkr.co/edit/UqQtxj?p=preview)

### 预定义参数

有一个自定义验证器来检查感叹号可能是有帮助的，但如果你需要检查一些其他形式的标点符号怎么办？ 你可能需要一遍又一遍地写做相同的事情。

考虑前面的例子`Validators.minLength(5)`。 如果验证器只是一个函数，它们如何允许一个参数来控制长度？ 简单，真的。 这不是Angular或TypeScript的一个戏法，它是简单的JavaScript闭包。

```typescript
function minLength(minimum) {
  return function(input) {
    return input.value.length >= minimum ? null : { minLength: true };
  };
}
```

假设你有一个函数，它接受一个“最小”参数并返回另一个函数。 从内部定义和返回的函数成为验证器。 闭包引用允许您记住最终调用验证器时的最小值。

让我们将这种思路应用到`PunctuationValidator`。

*app/login-form.component.ts*

```typescript
function hasPunctuation(punctuation: string, errorType: string) {
  return function(input: FormControl) {
    return input.value.indexOf(punctuation) >= 0 ?
        null :
        { [errorType]: true };
  };
}

// ...

this.password = new FormControl('', [
  Validators.required,
  hasPunctuation('&', 'ampersandRequired')
]);
```

*app/login-form.component.html*

```html
<!-- ... -->
<div [hidden]="!password.hasError('ampersandRequired')">
  You must have an &amp; in your password.
</div>
<!-- ... -->
```

[View Example](https://plnkr.co/edit/m5BpOX?p=preview)

### 使用其他输入验证输入

记住前面提到的：输入可以通过`.root`访问他们的父上下文。 因此，复杂的验证可以通过`.root`在表单上获取。

```typescript
function duplicatePassword(input: FormControl) {
  if (!input.root || !input.root.controls) {
    return null;
  }

  const exactMatch = input.root.controls.password === input.value;
  return exactMatch ? null : { mismatchedPassword: true };
}

// ...
this.duplicatePassword = new FormControl('', [
  Validators.required,
  duplicatePassword
]);
```

[View Example](https://plnkr.co/edit/OVL1Vl?p=preview)