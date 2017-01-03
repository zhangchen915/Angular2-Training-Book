#变化检测类
在运行时，Angular 2将创建被称为变化检测器的特殊类，一个用于我们定义的每个组件。在这种情况下，Angular将创建两个类：`AppComponent`和`AppComponent_ChangeDetector`。

变化检测器的目标是知道从上一次变化检测过程运行以来，组件的模板中使用的哪些模型属性已经改变。

为了知道，Angular创建了一个适当的变化检测器类的实例和一个链接到它应该检查的组件。

在我们的示例中，因为我们只有一个`MainComponent`和`MovieComponent`的实例，我们将只有一个`MainComponent_ChangeDetector`和`MovieComponent_ChangeDetector`的实例。

下面的代码片段是一个`MainComponent_ChangeDetector`类的概念模型。

```typescript
class MainComponent_ChangeDetector {

  constructor(
    public previousSlogan: string,
    public previousTitle: string,
    public previousActor: Actor,
    public movieComponent: MovieComponent
  ) {}

  detectChanges(slogan: string, title: string, actor: Actor) {
    if (slogan !== this.previousSlogan) {
      this.previousSlogan = slogan;
      this.movieComponent.slogan = slogan;
    }
    if (title !== this.previousTitle) {
      this.previousTitle = title;
      this.movieComponent.title = title;
    }
    if (actor !== this.previousActor) {
      this.previousActor = actor;
      this.movieComponent.actor = actor;
    }
  }
}
```
因为在我们的`MainComponent`的模板中我们引用了三个变量（slogan，title和actor），我们的变化检测器将有三个属性来存储这三个属性的“旧”值，加上一个对`MainComponent`实例的引用的“watch”。 当变化检测过程想知道我们的`MainComponent`实例是否已经改变时，它将运行`detectChanges`方法传递当前模型值以与旧的模型值进行比较。 如果检测到更改，组件将更新。

> 免责声明：这只是一个概念概述了如何更改检测器类工作; 实际实现可能不同。

#变化检测策略：默认
默认情况下，Angular为我们的应用程序中的每个组件定义了一定的变化检测策略。为了使这个定义显式，我们可以使用`@Component`装饰器的`changeDetection`属性。

*app/movie.component.ts*

```typescript
// ...
import {ChangeDetectionStrategy} from '@angular/core';

@Component({
  // ...
  changeDetection: ChangeDetectionStrategy.Default
})
export class MovieComponent {
  // ...
}
```
让我们看看当用户使用默认策略时点击“Change Actor Properties”按钮时会发生什么。

如前所述，改变由事件触发，并且改变的传播在两个阶段进行：应用阶段和改变检测阶段。

**阶段1（申请）：**
在第一阶段，应用程序（我们的代码）负责响应一些事件更新模型。 在这种情况下，属性`actor.firstName`和`actor.lastName`被更新。

**阶段2（变化检测）：**

更改检测始终从根组件（在这种情况下是`AppComponent`）开始，并检查是否有任何绑定到其模板的模型属性已更改，将每个属性的旧值（触发事件之前）与新值（模型更新后）进行比较。。 AppComponent模板具有对三个属性，`slogan`, `title` 和`	actor`的引用，因此由其相应的更改检测器进行的比较将如下所示：

- Is `slogan !== previousSlogan`? 不，这是一样的。
- Is `title !== previousTitle`?不，这是一样的。
- Is `actor !== previousActor`? 不，这是一样的。

注意，即使我们改变`actor`对象的属性，我们总是使用同一个实例。因为我们做一个浅的比较，询问`if actor!== previousActor`的结果将永远是`false`，即使它的内部属性值确实改变了。尽管变化检测器不能找到任何变化，变化检测的**默认策略**是**遍历树的所有组件**，即使它们似乎没有被修改。

接下来，变化检测在组件层次结构中向下移动，并检查绑定到`MovieComponent`模板的属性进行类似的比较：

- Is `title !== previousTitle`? 不，这是一样的。
- Is `actorFirstName !== previousActorFirstName`? 是的，它已经改变了。
- Is `actorLastName !== previousActorLastName`? 是的，它已经改变了。

最后，Angular检测到绑定到模板的一些属性已更改，因此它将更新DOM以获取与模型同步的视图。

#性能影响
遍历所有树组件以检查更改可能是昂贵的。想象一下，我们有多个引用，而不是在`MainComponent`的模板中只有一个对`<movie>`的引用？
```html
<movie *ngFor="let movie of movies" [title]="movie.title" [actor]="movie.actor"></movie>`
```
如果我们的电影列表增长太大，我们的系统的性能将开始退化。我们可以将问题缩小到一个特定的比较：

* actor !== previousActor?

正如我们所学到的，这个结果不是很有用，因为我们可以改变对象的属性而不改变实例，比较的结果总是为`false`。因此，变化检测将必须检查每个子组件以查看该对象（`firstName`或`lastName`）的任何属性是否已更改。

如果我们可以找到一种方法来指示变化检测，我们的`MovieComponent`仅依赖于其输入，这些输入是不可变的？总之，我们试图保证当我们改变`actor`对象的任何属性时，我们最终得到一个不同的`Actor`实例，所以比较`actor!== previousActor`总是返回`true`。另一方面，如果我们没有更改任何属性，我们不会创建一个新的实例，所以相同的比较将返回`false`。

如果上述条件可以保证（每次任何其属性改变时创建一个新对象，否则我们保持相同的对象），当检查`MovieComponent`的输入时有以下结果：

- Is `title !== previousTitle`? 不，这是一样的。
- Is `actor !== previousActor`?不，这是一样的。

那么我们可以跳过组件模板的内部检查，因为我们现在确定内部没有什么变化，没有必要更新DOM。这将提高变更检测系统的性能，因为更少的比较必须通过应用程序传播更改。