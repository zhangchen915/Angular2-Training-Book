#使用多个结构指令

 有时我们会想多结构指令结合在一起，像使用迭代ngFor，但想要做的ngIf，以确保该值相匹配的部分或多个条件。组合结构指令可能导致意想不到的结果，因此Angular 2要求模板一次只能绑定到一个指令。要应用多个指令，我们必须扩展含糖语法或嵌套模板标签。
```typescript
 @Component({
   selector: 'app',
   template: `
     <template ngFor [ngForOf]="[1,2,3,4,5,6]" let-item>
       <div *ngIf="item > 3">
         {{item}}
       </div>
     </template>
   `
 })
```
[View Example](https://plnkr.co/edit/V2nWlGOwIITPrUDksGNG?p=preview)

下面的选项卡示例可以使用ngFor和ng Switch，如果选项卡标题和内容被抽象到组件类中。

```typescript
 import {Component} from '@angular/core';
 
 @Component({
   selector: 'app',
   template: `
     <div class="tabs-selection">
       <tab
         *ngFor="let tab of tabs; let i = index"
         [active]="isSelected(i)"
         (click)="setTab(i)">
 
         {{ tab.title }}
       </tab>
     </div>
 
     <div [ngSwitch]="tabNumber">
       <template ngFor [ngForOf]="tabs" let-tab let-i="index">
         <tab-content *ngSwitchCase="i">
           {{tab.content}}
         </tab-content>
       </template>
       <tab-content *ngSwitchDefault>Select a tab</tab-content>
     </div>
   `
 })
 export class AppComponent {
   tabNumber: number = -1;
 
   tabs = [
     { title: 'Tab 1', content: 'Tab content 1' },
     { title: 'Tab 2', content: 'Tab content 2' },
     { title: 'Tab 3', content: 'Tab content 3' },
   ];
 
   setTab(num: number) {
     this.tabNumber = num;
   }
 
   isSelected(num: number) {
     return this.tabNumber === i;
   }
 }
```
[View Example](https://plnkr.co/edit/YOT4G4buUZduwvVi8cMA?p=preview)