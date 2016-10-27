#使用指令设置属性
我们可以使用属性指令通过使用@HostBinding装饰器来影响主机节点上的属性的值。

@HostBinding装饰器允许我们在指令的host元素上编程设置属性值。 它类似于模板中定义的属性绑定，除了它专门定位到host元素。 对每个变化检测周期检查绑定，因此如果需要，它可以动态地改变。
例如，假设我们要为按钮创建一个指令，当我们按下它时动态添加一个类。 这可能看起来像：
```ts
import { Directive, HostBinding, HostListener } from '@angular/core';

@Directive({
  selector: '[buttonPress]'
})
export class ButtonPressDirective {
  @HostBinding('attr.role') role = 'button';
  @HostBinding('class.pressed') isPressed: boolean;

  @HostListener('mousedown') hasPressed() {
    this.isPressed = true;
  }
  @HostListener('mouseup') hasReleased() {
    this.isPressed = false;
  }
}
```
请注意，对于@HostBinding的两个用例，我们传递一个字符串值，我们想要影响的属性。 如果我们不向装饰器提供字符串，那么将使用类成员的名称。
在第一个@HostBinding中，我们静态地将角色属性设置为button。 对于第二个示例，当isPressed为true时，将应用按下的类。
提示：尽管不太常见，如果需要，@HostBinding也可以应用于组件。