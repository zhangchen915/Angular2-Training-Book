#变化检测如何工作
让我们看看变化检测如何用一个简单的例子。
我们将创建一个简单的MovieApp来显示一个电影的信息。 这个应用程序将只包含两个组件：MovieComponent显示有关电影的信息和MainComponent，它保存对电影的引用按钮执行一些操作。
我们的主要组件将有三个属性：应用程序的口号，电影的标题和主角。 最后两个属性将被传递到模板中引用的MovieComponent元素。
app/main.component.ts
```ts
import {Component} from '@angular/core';
import {MovieComponent} from './movie.component';
import {Actor} from './actor.model';

@Component({
  selector: 'main',
  template: `
    <h1>MovieApp</h1>
    <p>{{ slogan }}</p>
    <button type="button" (click)="changeActorProperties()">Change Actor Properties</button>
    <button type="button" (click)="changeActorObject()">Change Actor Object</button>
    <movie [title]="title" [actor]="actor"></movie>`
})
export class MainComponent {
  slogan: string = 'Just movie information';
  title: string = 'Terminator 1';
  actor: Actor = new Actor('Arnold', 'Schwarzenegger');

  changeActorProperties() {
    this.actor.firstName = 'Nicholas';
    this.actor.lastName = 'Cage';
  }

  changeActorObject() {
    this.actor = new Actor('Bruce', 'Willis');
  }
}
```
在上面的代码片段中，我们可以看到我们的组件定义了两个触发不同方法的按钮。 changeActorProperties将通过直接更改actor对象的属性来更新电影的主角。 相反，方法changeActorObject将通过创建一个完全新的Actor类实例来改变actor的信息。
Actor模型非常简单，它只是一个定义了actor的firstName和lastName的类。
app/actor.model.ts
```ts
export class Actor {
  constructor(
    public firstName: string,
    public lastName: string) {}
}
```
最后，MovieComponent显示了MainComponent在其模板中提供的信息。
app/movie.component.ts
```ts
import {Component, Input} from '@angular/core';
import {Actor} from './actor.model';

@Component({
  selector: 'movie',
  styles: ['div {border: 1px solid black}'],
  template: `
    <div>
      <h3>{{ title }}</h3>
      <p>
        <label>Actor:</label>
        <span>{{actor.firstName}} {{actor.lastName}}</span>
      </p>
    </div>`
})
export class MovieComponent {
  @Input() title: string;
  @Input() actor: Actor;
}
```
    