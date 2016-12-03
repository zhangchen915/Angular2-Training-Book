#NgSwitch 指令
ngSwitch实际上包括两个指令，一个属性指令和一个结构指令。 它非常类似于JavaScript和其他编程语言中的switch语句，但是在模板中。
```ts
@Component({
  selector: 'app',
  template: `
    <div class="tabs-selection">
      <tab [active]="isSelected(1)" (click)="setTab(1)">Tab 1</tab>
      <tab [active]="isSelected(2)" (click)="setTab(2)">Tab 2</tab>
      <tab [active]="isSelected(3)" (click)="setTab(3)">Tab 3</tab>
    </div>

    <div [ngSwitch]="tab">
      <tab-content *ngSwitchCase="1">Tab content 1</tab-content>
      <tab-content *ngSwitchCase="2">Tab content 2</tab-content>
      <tab-content *ngSwitchCase="3"><tab-3></tab-3></tab-content>
      <tab-content *ngSwitchDefault>Select a tab</tab-content>
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
View Example

这里我们看到ngSwitch属性指令附加到一个元素。 该表达式绑定到指令定义什么将在开关结构指令中进行比较。 如果绑定到ngSwitchCase的表达式匹配给予ngSwitch的表达式，那么将创建这些组件，并销毁其他组件。 如果没有匹配的情况，则会创建与其绑定的ngSwitchDefault的组件，其他组件将被销毁。 注意，可以使用ngSwitchCase来匹配多个组件，在这些情况下，将创建所有匹配的组件。 由于组件被创建或销毁，请注意这样做的成本。