# 模版驱动表单

在Angular 2中构建表单最直接的方法是利用为您提供的指令。

首先，考虑一个典型的表单：

```HTML
<form method="POST" action="/register" id="signup-form">
  <label for="email">Email</label>
  <input type="text" name="email" id="email">

  <label for="password">Password</label>
  <input type="password" name="password" id="password">

  <button type="submit">Sign Up</button>
</form>
```

Angular 2已经为你提供了一个`form`指令，并形成在封面下操作的相关指令，如输入等。 对于基本的实现，我们只需要添加一些属性，并确保我们的组件知道如何处理数据。	

*index.html*

```html
<signup-form>Loading...</signup-form>
```

*signup-form.component.html*

```html
<form #signupForm="ngForm" (ngSubmit)="registerUser(signupForm)">
  <label for="email">Email</label>
  <input type="text" name="email" id="email" ngModel>

  <label for="password">Password</label>
  <input type="password" name="password" id="password" ngModel>

  <button type="submit">Sign Up</button>
</form>
```

*signup-form.component.ts*

```typescript
import { Component } from '@angular/core';
import { NgForm } from '@angular/forms';

@Component({
  selector: 'app-signup-form',
  templateUrl: 'app/signup-form.component.html',
})
export class SignupFormComponent {
  registerUser(form: NgForm) {
    console.log(form.value);
    // {email: '...', password: '...'}
    // ...
  }
}
```