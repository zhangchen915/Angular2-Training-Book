#从模板访问子组件
在我们的模板中，我们可能发现自己需要访问由我们用来构建自己的组件的子组件提供的值。
最直接的例子可能是处理形式或输入：

**app/app.component.html*

```html
<section >
  <form #myForm="ngForm" (ngSubmit)="onSubmit(myForm)">
    <label for="name">Name</label>
    <input type="text" name="name" id="name" ngModel>
    <button type="submit">Submit</button>
  </form>
  Form Value: {{formValue}}
</section>
```

*app/app.component.ts*

```typescript
import { Component } from '@angular/core';

@Component({
  selector: 'rio-app',
  templateUrl: 'app/app.component.html'
})
export class AppComponent {
  formValue = JSON.stringify({});

  onSubmit (form: NgForm) {
    this.formValue = JSON.stringify(form.value);
  }
}
```

[View Example](https://plnkr.co/edit/hfv5RC?p=preview)

这不是一个只有形式或输入的神奇功能，而是一种引用模板中子组件实例的方法。使用该引用，您可以访问该组件的公共属性和方法。

*app/app.component.html*

```html
<rio-profile #profile></rio-profile>
My name is {{ profile.name }}
```

*app/profile.component.ts*

```javascript
@Component({
  selector: 'rio-profile',
  templateUrl: 'app/profile.component.html'
})
export class ProfileComponent {
  name = 'John Doe';
}
```

[View Example](https://plnkr.co/edit/wEFOta?p=preview)

还有其他访问和连接子组件的方法，但是如果你只需要引用一个孩子的属性或方法，这可以是一个简单和直接的方法。