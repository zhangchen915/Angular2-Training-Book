#从模板访问子组件
在我们的模板中，我们可能发现自己需要访问由我们用来构建自己的组件的子组件提供的值。
最直接的例子可能是处理形式或输入：

app / my-example.component.html
```html
<section >
  <form #myForm="ngForm" (ngSubmit)="submitForm(myForm)">
    <label for="name">Name</label>
    <input type="text" name="name" id="name" ngModel>
    <button type="submit">Submit</button>
  </form>
  Form value: {{formValue}}
</section>
app / my-example.component.ts
import {Component} from '@angular/core';

@Component({
  selector: 'my-example',
  templateUrl: 'app/my-example.component.html'
})
export class MyExampleComponent {
  formValue: string = JSON.stringify({});
  submitForm (form: NgForm) {
    this.formValue = JSON.stringify(form.value);
  }
}
```
查看示例
这不是一个只有形式或输入的魔法特征，而是一种引用你的模板中子组件实例的方法。使用该引用，您可以访问该组件的公共属性和方法。
```html
<my-profile #profile></my-profile>
My name is {{ profile.name }}
@Component({
  selector: 'my-profile',
  templateUrl: 'app/my-profile.component.html'
})
export class MyProfile {
  name: string = 'John Doe';
}
```
查看示例
还有其他访问和连接子组件的方法，但是如果你只需要引用一个孩子的属性或方法，这可以是一个简单直接的方法。