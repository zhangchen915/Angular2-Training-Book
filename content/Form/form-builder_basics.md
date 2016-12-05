# FormBuilder 基础

开始使用`FormBuilder`，我们必须首先确保我们正在使用正确的指令和正确的类，以利用程序式的表单。 为此，我们需要确保在应用程序模块的引导阶段中导入`FormsModule`和`ReactiveFormsModule。`
这将让我们访问组件，指令和提供程序，如`FormBuilder`，`FormGroup`和`FormControl`
在我们的例子中，将构建一个登录表单，我们看下面的内容：

*app/login-form.component.ts*

```typescript
import {Component} from '@angular/core';
import {
  FormBuilder,
  FormControl
} from '@angular/forms';

@Component({
  selector: 'login-form',
  templateUrl: 'app/login-form.component.html',
})
export class LoginForm {
  loginForm: FormGroup;
  username: FormControl;
  password: FormControl;

  constructor(builder: FormBuilder) {
    this.username = new FormControl('', []);
    this.password = new FormControl('', []);
    this.loginForm = builder.group({
      username: this.username,
      password: this.password
    });
  }

  login() {
    console.log(this.loginForm.value);
    // Attempt Logging in...
  }
}
```

*app/login-form.component.html*

```html
<form [formGroup]="loginForm" (ngSubmit)="login()">
  <label for="username">username</label>
  <input type="text" name="username" id="username" [formControl]="username">

  <label for="password">password</label>
  <input type="password" name="password" id="password" [formControl]="password">

  <button type="submit">log in</button>
</form>
```

[View Example](https://plnkr.co/edit/o1Gfqg?p=preview)

### `FormControl`

请注意，`FormControl`类被分配到类似命名的字段，无论是在此还是在`FormBuilder＃group({ })`方法中。 这主要是为了方便访问。 通过保存对FormControl实例的引用，您可以访问模板中的输入，而无需引用表单本身。 否则，可以通过使用`loginForm.controls.username`和`loginForm.controls.password`在模板中访问表单字段。 同样，此情况下的任何`FormControl`实例都可以通过使用其.root属性（例如`username.root.controls.password`）访问其父组。

> 确保root和控件在使用之前存在。

`FormControl`需要两个属性：初始值和验证器列表。 现在，我们没有验证。 这将在后续步骤中添加。