# NgClass 指令

`ngClass`指令更改绑定到其附加的组件或元素的`class`属性。有几种不同的方式使用指令。

## 绑定一个字符串

我们可以直接将一个字符串绑定到属性。这就像添加一个html`class`属性一样。

```typescript
@Component({
  selector: 'app-class-as-string',
  template: `
    <p ngClass="centered-text underlined" class="orange">
      <ng-content></ng-content>
    </p>
  `,
  styles: [`
    .centered-text {
      text-align: center;
    }

    .underlined {
      border-bottom: 1px solid #ccc;
    }

    .orange {
      color: orange;
    }
  `]
})
export class ClassAsStringComponent {
}
```

[View Example](https://plnkr.co/edit/uUtjY1Qlkx5dOB8gsqCm?p=preview)

在这种情况下，我们直接绑定一个字符串，所以我们避免使用方括号包装指令。还要注意`ngClass`使用`class`属性来组合最终的类。

## 绑定一个数组

```typescript
@Component({
  selector: 'app-class-as-array',
  template: `
    <p [ngClass]="['warning', 'big']">
      <ng-content></ng-content>
    </p>
  `,
  styles: [`
    .warning {
      color: red;
      font-weight: bold;
    }

    .big {
      font-size: 1.2rem;
    }
  `]
})
export class ClassAsArrayComponent {
}
```

[View Example](https://plnkr.co/edit/uUtjY1Qlkx5dOB8gsqCm?p=preview)

在这里，由于我们使用表达式绑定到`ngClass`指令，我们需要在方括号中包装指令名称。当你想有一个函数放在适用的类名列表时，传入数组是很有用的。

## 绑定一个对象

最后，一个对象可以绑定到指令。如果该属性为true，Angular 2将该对象的每个属性名称应用于组件。

```typescript
@Component({
  selector: 'app-class-as-object',
  template: `
    <p [ngClass]="{ card: true, dark: false, flat: flat }">
      <ng-content></ng-content>
      <br>
      <button type="button" (click)="flat=!flat">Toggle Flat</button>
    </p>
  `,
  styles: [`
    .card {
      border: 1px solid #eee;
      padding: 1rem;
      margin: 0.4rem;
      font-family: sans-serif;
      box-shadow: 2px 2px 2px #888888;
    }

    .dark {
      background-color: #444;
      border-color: #000;
      color: #fff;
    }

    .flat {
      box-shadow: none;
    }
  `]
})
export class ClassAsObjectComponent {
  flat: boolean = true;
}
```

[View Example](https://plnkr.co/edit/uUtjY1Qlkx5dOB8gsqCm?p=preview)

在这里我们可以看到，由于对象的`card` 和`flat`属性是true，这些classes将会生效，但由于`dark`是false，它不会生效。