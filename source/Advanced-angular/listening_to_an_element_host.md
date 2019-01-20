#监听Host元素

监听Host元素 - 也就是指令附加的DOM元素 - 是指令扩展组件或元素行为的主要方法。 以前，我们看到了它的常见用例。

```typescript
@Directive({
  selector: '[appMyDirective]'
})
class MyDirective {
  @HostListener('click', ['$event'])
  onClick() {}
}
```
我们还可以通过在侦听器中添加目标来响应外部事件，例如从`window` 或 `document`。
```typescript
@Directive({
  selector: `[appHighlight]`
})
export class HighlightDirective {
  constructor(private el: ElementRef, private renderer: Renderer) { }

  @HostListener('document:click', ['$event'])
  handleClick(event: Event) {
    if (this.el.nativeElement.contains(event.target)) {
      this.highlight('yellow');
    } else {
      this.highlight(null);
    }
  }

  highlight(color) {
    this.renderer.setElementStyle(this.el.nativeElement, 'backgroundColor', color);
  }
}
```
[查看示例](https://plnkr.co/edit/HXJwSb2zvrhHy0NmBAzb?p=preview)

> 虽然不太常见，我们也可以使用`@HostListener`，如果要在组件的Host 元素上注册侦听器的话。

## Host元素

Host元素的概念适用于指令和组件。

对于一个指令，这个概念是相当简单的。 将您的指令属性放在哪个模板标记被认为是Host元素。如果我们像上面这样实现`HighlightDirective`：
```html
<div>
  <p highlight>
    <span>Text to be highlighted</span>
  </p>
</div>
```
`<p>`标记将被视为Host元素。 如果我们使用自定义TextBox组件作为Host，代码将如下所示：

```html
<div>
  <my-text-box highlight>
    <span>Text to be highlighted</span>
  </my-text-box>
</div>
```
在组件的上下文中，Host元素是您通过组件配置中的选择器字符串创建的标记。 对于上面示例中的`TextBoxComponent`，组件类的上下文中的Host元素将是`<my-text-box>`标记。