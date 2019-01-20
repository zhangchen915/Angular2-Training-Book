#NgSwitch 指令
`ngSwitch`实际上包括两个指令，一个属性指令和一个结构指令。 它非常类似于JavaScript和其他编程语言中的switch语句，但是在模板中。
```typescript
@Component({
  selector: 'app-root',
  template: `
    <div class="tabs-selection">
      <app-tab [active]="isSelected(1)" (click)="setTab(1)">Tab 1</app-tab>
      <app-tab [active]="isSelected(2)" (click)="setTab(2)">Tab 2</app-tab>
      <app-tab [active]="isSelected(3)" (click)="setTab(3)">Tab 3</app-tab>
    </div>

    <div [ngSwitch]="tab">
      <app-tab-content *ngSwitchCase="1">Tab content 1</app-tab-content>
      <app-tab-content *ngSwitchCase="2">Tab content 2</app-tab-content>
      <app-tab-content *ngSwitchCase="3"><app-tab-3></app-tab-3></app-tab-content>
      <app-tab-content *ngSwitchDefault>Select a tab</app-tab-content>
    </div>
  `
})
export class AppComponent {
  tab: number = 0;

  setTab(num: number) {
    this.tab = num;
  }

  isSelected(num: number) {
    return this.tab === num;
  }
}
```
[View Example](https://plnkr.co/edit/QWxD0DIZi6QiISafwfgu?p=preview)

这里我们看到`ngSwitch`属性指令附加到一个元素。 该表达式绑定到指令定义什么将在 switch 指令中进行比较。 如果绑定到`ngSwitchCase`的表达式匹配给予`ngSwitch`的表达式，那么将创建这些组件，并销毁其他组件。 如果没有匹配的情况，则会创建与其绑定的`ngSwitchDefault`的组件，其他组件将被销毁。 注意，可以使用`ngSwitchCase`来匹配多个组件，在这些情况下，将创建所有匹配的组件。 由于组件被创建或销毁，请注意这样做的成本。