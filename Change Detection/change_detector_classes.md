#变化检测类
在运行时，Angular 2将创建被称为变化检测器的特殊类，一个用于我们定义的每个组件。 在这种情况下，Angular将创建两个类：MainComponent_ChangeDetector和MovieComponent_ChangeDetector。
变化检测器的目标是知道从上一次变化检测过程运行以来，组件的模板中使用的哪些模型属性已经改变。
为了知道，Angular创建了一个适当的变化检测器类的实例和一个链接到它应该检查的组件。
在我们的示例中，因为我们只有一个MainComponent和MovieComponent的实例，我们将只有一个MainComponent_ChangeDetector和MovieComponent_ChangeDetector的实例。
下面的代码片段是一个MainComponent_ChangeDetector类的概念模型。
```ts
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
因为在我们的MainComponent的模板中我们引用了三个变量（slogan，title和actor），我们的变化检测器将有三个属性来存储这三个属性的“旧”值，加上一个对MainComponent实例的引用， 看”。 当变化检测过程想知道我们的MainComponent实例是否已经改变时，它将运行方法detectChanges传递当前模型值以与旧的模型值进行比较。 如果检测到更改，组件将更新。

> 免责声明：这只是一个概念概述了如何更改检测器类工作; 实际实现可能不同。

#变化检测策略：默认
默认情况下，Angular为我们的应用程序中的每个组件定义了一定的变化检测策略。为了使这个定义显式，我们可以使用@Component装饰器的属性changeDetection。
app/movie.component.ts
```ts
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
让我们看看当用户使用默认策略时点击“更改Actor属性”按钮时会发生什么。
如前所述，改变由事件触发，并且改变的传播在两个阶段进行：应用阶段和改变检测阶段。
阶段1（申请）：
在第一阶段，应用程序（我们的代码）负责响应一些事件更新模型。 在这种情况下，属性actor.firstName和actor.lastName被更新。
阶段2（变化检测）：
现在我们的模型已更新，Angular必须使用更改检测更新模板。
更改检测始终从根组件（在本例中为MainComponent）开始，并检查是否有任何绑定到其模板的模型属性已更改，将每个属性的旧值（触发事件之前）与新属性 模型更新）。 MainComponent模板具有对三个属性，口号，标题和actor的引用，因此由其相应的变化检测器进行的比较将如下所示：

slogan!== previousSlogan？不，这是一样的。
title!== previousTitle？不，这是一样的。
actor!== previousActor？不，这是一样的。
注意，即使我们改变actor对象的属性，我们总是使用同一个实例。因为我们做一个浅的比较，询问if actor!== previousActor的结果将永远是假的，即使它的内部属性值确实改变了。即使变化检测器不能找到任何变化，变化检测的默认策略是遍历树的所有组件，即使它们似乎没有被修改。
接下来，变化检测在组件层次结构中向下移动，并检查绑定到MovieComponent模板的属性进行类似的比较：
title!== previousTitle？不，这是一样的。
actorFirstName!== previousActorFirstName？是的，它已经改变了。
actorLastName!== previousActorLastName？是的，它已经改变了。
最后，Angular检测到绑定到模板的一些属性已更改，因此它将更新DOM以获取与模型同步的视图。

#性能影响
遍历所有树组件以检查更改可能是昂贵的。想象一下，我们有多个引用，而不是在MainComponent的模板中只有一个对`<movie>`的引用？
```html
<movie *ngFor="let movie of movies" [title]="movie.title" [actor]="movie.actor"></movie>`
```
如果我们的电影列表增长太大，我们的系统的性能将开始退化。我们可以将问题缩小到一个特定的比较：

* actor !== previousActor?

正如我们所学到的，这个结果不是很有用，因为我们可以改变对象的属性而不改变实例，比较的结果总是为假。因此，变化检测将必须检查每个子组件以查看该对象（firstName或lastName）的任何属性是否已更改。
如果我们可以找到一种方法来指示变化检测，我们的MovieComponent仅依赖于其输入，这些输入是不可变的？总之，我们试图保证当我们改变actor对象的任何属性时，我们最终得到一个不同的Actor实例，所以比较actor!== previousActor总是返回true。另一方面，如果我们没有更改任何属性，我们不会创建一个新的实例，所以相同的比较将返回false。
如果上述条件可以保证（每次任何其属性改变时创建一个新对象，否则我们保持相同的对象），当检查MovieComponent的输入时有以下结果：
title!== previousTitle？不，这是一样的。
actor!== previousActor？不，这是一样的。
那么我们可以跳过组件模板的内部检查，因为我们现在确定内部没有什么变化，没有必要更新DOM。这将提高变更检测系统的性能，因为更少的比较必须通过应用程序传播更改。