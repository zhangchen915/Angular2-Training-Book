# NgStyle 指令

Angular 2提供了一个内置的指令`ngStyle`来修改组件或元素的`style`属性。这里有一个例子：

```typescript
@Component({
  selector: 'app-style-example',
  template: `
    <p style="padding: 1rem"
      [ngStyle]="{
        'color': 'red',
        'font-weight': 'bold',
        'borderBottom': borderStyle
      }">
      <ng-content></ng-content>
    </p>
  `
})
export class StyleExampleComponent {
  borderStyle = '1px solid black';
}
```

[View Example](https://plnkr.co/edit/akK3Gw8W6EgUQ4PRQp4h?p=preview)

请注意，绑定指令的方式与组件属性绑定完全相同。 这里，我们将一个表达式和一个对象字面量绑定到`ngStyle`指令，因此指令名称必须用方括号括起来。 `ngStyle`接受一个对象，其属性和值定义该元素的样式。 在这种情况下，我们可以看到，当指定`style`属性时，可以使用短横线式（kebab case）和小驼峰式（lower camel case）命名。 还要注意，在对元素进行样式设计时，html的`style` 属性和Angular  `ngStyle`指令都是合并的。

我们可以将样式属性从模板中移除到组件中作为属性对象，然后使用属性绑定将其分配给`NgStyle`。 这允许对值的动态更改，而且让添加和删除样式属性更加灵活。

```typescript
@Component({
  selector: 'app-style-example',
  template: `
    <p style="padding: 1rem"
      [ngStyle]="alertStyles">
      <ng-content></ng-content>
    </p>
  `
})
export class StyleExampleComponent {
  borderStyle = '1px solid black';

  alertStyles = {
    'color': 'red',
    'font-weight': 'bold',
    'borderBottom': this.borderStyle
  };
}
```