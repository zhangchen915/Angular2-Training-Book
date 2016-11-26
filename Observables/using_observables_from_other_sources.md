#使用其他来源的Observable
在上面的例子中，我们从头开始创建Observable，这在理解Observable的解剖结构时特别有用。

但是，我们将经常从回调，promise，事件，集合或使用API上可用的许多运算符创建Observable。

##Observable HTTP Events
任何Web应用程序中的常见操作是向服务器获取或发布数据。 Angular应用程序使用Http库来实现这一点，它以前使用Promises以异步方式操作。 更新的Http库现在合并了Observable，用于触发事件和获取新数据。 让我们快速浏览一下：
```ts
import {Component} from '@angular/core';
import {Http} from '@angular/http';
import 'rxjs/Rx';

@Component({
    selector: 'app',
    template: `
      <b>Angular 2 HTTP requests using RxJs Observables!</b>
      <ul>
        <li *ngFor="let doctor of doctors">{{doctor.name}}</li>
      </ul>

      `
})

export class MyApp {
  private doctors = [];

  constructor(http: Http) {
    http.get('http://jsonplaceholder.typicode.com/users/')
        .flatMap((data) => data.json())
        .subscribe((data) => {
          this.doctors.push(data);

        });
  }
}
```
这个基本示例概述了Http库常见例程如get，post，put和delete所有返回Observables，允许我们异步处理任何结果数据。

##Observable Form Events
让我们来看看Observables如何在Angular 2形式中使用。 表单中的每个字段都被视为一个Observable，我们可以订阅和监听对输入字段的值所做的任何更改。
```ts
import {Component} from '@angular/core';
import {FormControl, FormGroup, FormBuilder} from '@angular/forms';
import 'rxjs/add/operator/filter';
import 'rxjs/add/operator/map';

@Component({
    selector: 'app',
    template: `
      <form [formGroup]="coolForm">
            <input formControlName="email">
        </form>
      <div>
            <b>You Typed Reversed:</b> {{data}}
        </div>
    `
})

export class MyApp {

    email: FormControl;
    coolForm: FormGroup;
    data: string;

    constructor(private fb: FormBuilder) {
        this.email = new FormControl();

        this.coolForm = fb.group({
            email: this.email
        });

        this.email.valueChanges
        .filter(n=>n)
        .map(n=>n.split('').reverse().join(''))
        .subscribe(value => this.data = value);
    }
}
```
这里我们通过初始化一个新的FormControl字段并将其分组到一个绑定到coolForm HTML表单的FormGroup中来创建一个新的表单。 控件字段有一个属性.valueChanges，它返回一个我们可以订阅的Observable。 现在每当用户在字段中输入内容时，我们会立即得到它。