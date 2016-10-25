#投影

投影是Angular 2中非常重要的概念。它使开发人员能够构建可重用的组件，并使应用程序更具可扩展性和可维护性。为了说明，假设我们有一个ChildComponent这样的：
```ts
@Component({
    selector: 'child',
    template: `
      <div>
        <h4>Child Component</h4>
        {{ childContent }}
      </div>
    `
})
export class ChildComponent {
  childContent: string = "Default content";
}
```
如果我们要将{{childContent}}替换为提供给ChildComponent的任何HTML，我们应该怎么办？ 一个诱人的想法是定义一个包含文本的@Input，但如果你想提供样式化的HTML或其他组件呢？ 尝试使用@Input处理这个问题可能会很快弄乱，这是内容投影的地方。组件默认情况下支持投影，您可以使用ngContent指令将投影内容放置在模板中。
因此，改变ChildComponent使用投影：
```ts
import {Component, Input} from '@angular/core';

@Component({
  selector: 'child',
  template: `
    <h4>Child Component</h4>
    <ng-content></ng-content>
  `
})

class ChildComponent {}
```
然后，当我们使用ChildComponent在模板：
```html
<child>
  <p>My projected content.</p>
</child>
```
这告诉Angular，对于`<child>`的开始和结束标记之间出现的任何标记，放置在`<ng-content> </ng-content>`内。
当这样做，我们可以有其他组件，标记等投影在这里和ChildComponent不需要知道或关心提供什么。

[查看示例](http://plnkr.co/edit/8TD5tXVMOOBNrvYjfhrR?p=preview)

但是，如果我们有多个`<ng-content></ng-content>`，并希望指定预计的内容一定的位置ng-content？例如，对于前面的ChildComponent，如果我们想投影内容转换成一个额外的格式化header和footer部分：
```ts
import {Component, Input} from '@angular/core';

@Component({
  selector: 'child',
  template: `
    <h4>Child Component</h4>
    <ng-content select="header"></ng-content>
    <ng-content></ng-content>
    <ng-content select="footer"></ng-content>
  `
})
class ChildComponent {}
```
然后在模板中，我们可以使用指令，例如`<header>`，通过`select =“header”`指定投影内容到`ng-content`的位置：
```html
<div>
  <child>
    <header>Header Content</header>
    Main Content
    <footer>Footer Content</footer>
  </child>
</div>
```
除了使用指令，开发者还可以通过CSS类选择ng-content：
```html
<ng-content select=".class-select"></ng-content>
<div class="class-select">
  div with .class-select
</div>
```
[查看示例](http://plnkr.co/edit/SEPQUS4MqWCftbwjBBi8?p=preview)