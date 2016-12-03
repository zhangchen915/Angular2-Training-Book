#执行不变性

我们在前面的例子中做了一点。 我们告诉Angular我们所有的输入，包括actor对象，都是不可变的对象，但是我们继续更新它的属性，违反了不变性原则。 因此，我们最终在我们的模型和我们的视图之间出现同步问题。 一种强制不变性的方法是使用库Immutable.js。
因为在JavaScript原始类型，如字符串和数字是不可变的，通过定义，我们应该只处理我们正在使用的对象。 在这种情况下，actor对象。

这里有一个例子，比较可变的数组类型和不可变的字符串类型：

```typescript
var b = ['C', 'a', 'r'];
b[0] = 'B';
console.log(b) // ['B', 'a', 'r'] => The first letter changed, arrays are mutable

var a = 'Car';
a[0] = 'B';
console.log(a); // 'Car' => The first letter didn't change, strings are immutable
```

首先，我们需要使用命令安装immutable.js库：

```
npm install --save immutable
```

然后在我们的MainComponent中，我们导入库并使用它创建一个不可变的actor对象。

*app/main.component.ts*

```typescript
import {Component} from '@angular/core';
import {MovieComponent} from './movie.component';
import * as Immutable from 'immutable';

@Component({
  selector: 'main',
  template: `
    <h1>MovieApp</h1>
    <p>{{ slogan }}</p>
    <button type="button" (click)="changeActor()">Change Actor</button>
    <movie [title]="title" [actor]="actor"></movie>`
})
export class MainComponent {
  slogan: string = 'Just movie information';
  title: string = 'Terminator 1';
  actor: Immutable.Map<string, string> = Immutable.Map({firstName: 'Arnold', lastName: 'Schwarzenegger'});

  changeActor() {
    this.actor = this.actor.merge({firstName: 'Nicholas', lastName: 'Cage'});
  }
}
```

现在，我们不是创建一个Actor类的实例，而是使用Immutable.Map来定义一个不可变的对象。 因为this.actor现在是一个不可变的对象，我们不能直接改变它的内部属性（firstName和lastName）。 然而，我们可以做的是创建另一个基于actor的对象，这两个字段具有不同的值 - 这正是merge方法的作用。
因为当我们尝试改变actor时，我们总是得到一个新的对象，所以在我们的组件中有两个不同的方法没有意义。 我们删除了方法changeActorProperties和changeActorObject并创建了一个名为changeActor的新方法。
还必须对MovieComponent进行其他更改。 首先，我们需要将actor对象声明为不可变类型，并且在模板中，而不是尝试使用类似actor.firstName的语法直接访问对象属性，我们需要使用不可变的get方法。

*app/movie.component.ts*

```typescript
import {Component, Input} from '@angular/core';
import {ChangeDetectionStrategy} from '@angular/core';
import * as Immutable from 'immutable';

@Component({
  selector: 'movie',
  styles: ['div {border: 1px solid black}'],
  template: `
    <div>
      <h3>{{ title }}</h3>
      <p>
        <label>Actor:</label>
        <span>{{actor.get('firstName')}} {{actor.get('lastName')}}</span>
      </p>
    </div>`,
  changeDetection: ChangeDetectionStrategy.OnPush
})
export class MovieComponent {
  @Input() title: string;
  @Input() actor: Immutable.Map<string, string>;
}
```

使用这种模式，我们充分利用了“OnPush”变化检测策略，从而减少了Angular传播变化和获得模型和视图同步的工作量。 这提高了应用程序的性能。