# 验证响应式表单

从上一个登录表单构建，我们可以快速轻松地添加验证。

Angular 2提供了开箱即用的许多验证器。 它们可以与其余的依赖关系一起导入。

*app/login-form.component.ts*

```typescript
import { Component } from '@angular/core';
import { Validators, FormBuilder, FormControl } from '@angular/forms';

@Component({
  // ...
})
export class AppComponent {
  username = new FormControl('', [
    Validators.required,
    Validators.minLength(5)
  ]);

  password = new FormControl('', [Validators.required]);

  loginForm: FormGroup = this.builder.group({
    username: this.username,
    password: this.password
  });

  constructor(private builder: FormBuilder) { }

  login () {
    console.log(this.loginForm.value);
    // Attempt Logging in...
  }
}
```

*app/login-form.component.html*

```html
<form [formGroup]="loginForm" (ngSubmit)="login()">

  <div>
    <label for="username">username</label>

    <input
      type="text"
      name="username"
      id="username"
      [formControl]="username">

    <div [hidden]="username.valid || username.untouched">
      <div>
        The following problems have been found with the username:
      </div>

      <div [hidden]="!username.hasError('minlength')">
        Username can not be shorter than 5 characters.
      </div>
      <div [hidden]="!username.hasError('required')">
        Username is required.
      </div>
    </div>
  </div>
  <div >
    <label for="password">password</label>
    <input
      type="password"
      name="password"
      id="password" [formControl]="password">

    <div [hidden]="password.valid || password.untouched">
      <div>
        The following problems have been found with the password:
      </div>

      <div [hidden]="!password.hasError('required')">
        The password is required.
      </div>
    </div>
  </div>

  <button type="submit" [disabled]="!loginForm.valid">Log In</button>
</form>
```

注意，我们对字段和表单本身添加了相当稳健的验证，只使用内置验证器和一些模板逻辑。

[View Example](https://plnkr.co/edit/kr8Q41?p=preview)

我们使用`.valid`和`.untouched`来确定是否需要显示错误，而字段是必需的，没有理由告诉用户如果字段尚未被访问，该值是错误的。

对于内置验证，我们在表单元素上调用`.hasError()`，我们传递一个字符串，它表示我们包含的验证器函数。 仅当此测试返回true时，才会显示错误消息。