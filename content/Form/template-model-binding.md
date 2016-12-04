# Using Template Model Binding

### 单向绑定

如果您需要具有默认值的表单，可以开始使用ngModel的值绑定语法。

*app/signup-form.component.html*

```html
<form #signupForm="ngForm" (ngSubmit)=register(signupForm)>

  <label for="username">Username</label>
  <input type="text" name="username" id="username" [ngModel]="generatedUser">

  <label for="email">Email</label>
  <input type="email" name="email" id="email" ngModel>

  <button type="submit">Sign Up</button>
</form>
```

*app/signup-form.component.ts*

```typescript
import { Component } from '@angular/core';
import { NgForm } from '@angular/forms';
// ...

@Component({ /* ... */ })
export class SignupForm {
  generatedUser: string = generateUniqueUserID();

  register(form: NgForm) {
    console.log(form.value);
    // ...
  }
}
```

### 双向绑定

虽然Angular 2默认情况下假定单向绑定，但如果需要，双向绑定仍然可用。
为了能够访问模板驱动表单中的双向绑定，请使用“Banana-Box”语法`([(ngModel)] ="propertyName")`。
请务必声明组件上需要的所有属性。

```typescript
<form #signupForm="ngForm" (ngSubmit)=register(signupForm)>

  <label for="username">Username</label>
  <input type="text" name="username" id="username" [(ngModel)]="username">

  <label for="email">Email</label>
  <input type="email" name="email" id="email" [(ngModel)]="email">

  <button type="submit">Sign Up</button>
</form>

import { Component } from '@angular/core';
import { NgForm } from '@angular/forms';
// ...

@Component({ /* ... */ })
export class SignUpForm {
  username: string = generateUniqueUserID();
  email: string = '';

  register(form: NgForm) {
    console.log(form.value.username);
    console.log(this.username);
    // ...
  }
}
```