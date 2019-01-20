#NgFor 指令
`ngFor`指令是通过使用可迭代的每个项作为模板的上下文来重复模板的一种方式。
```typescript
@Component({
  selector: 'app-root',
  template: `
    <app-for-example *ngFor="let episode of episodes" [episode]="episode">
      {{episode.title}}
    </app-for-example>
  `
})
export class AppComponent {
  episodes = [
    { title: 'Winter Is Coming', director: 'Tim Van Patten' },
    { title: 'The Kingsroad', director: 'Tim Van Patten' },
    { title: 'Lord Snow', director: 'Brian Kirk' },
    { title: 'Cripples, Bastards, and Broken Things', director: 'Brian Kirk' },
    { title: 'The Wolf and the Lion', director: 'Brian Kirk' },
    { title: 'A Golden Crown', director: 'Daniel Minahan' },
    { title: 'You Win or You Die', director: 'Daniel Minahan' },
    { title: 'The Pointy End', director: 'Daniel Minahan' }
  ];
}
```
[View Example](https://plnkr.co/edit/dXU4K13piTYotDX5Nhi6?p=preview)

`ngFor`指令与我们看到的其他指令有不同的语法。 如果你熟悉`for...of`语句，你会注意到，他们几乎相同。 `ngFor`允许您指定要迭代的iterable对象，以及在范围内引用每个项的名称。 在我们的示例中，您可以看到该`episode` 可用于插值以及属性绑定。 该指令做一些额外的解析，所以当它扩展到模板形式，它看起来有点不同：

```typescript
@Component({
  selector: 'app',
  template: `
    <template ngFor [ngForOf]="episodes" let-episode>
      <app-for-example [episode]="episode">
        {{episode.title}}
      </app-for-example>
    </template>
  `
})
```
[View Example](https://plnkr.co/edit/dXU4K13piTYotDX5Nhi6?p=preview)

请注意，模板元素上有一个奇怪的let-episode属性。 ngFor指令在其范围内提供一些变量作为上下文。 let-episode是一个上下文绑定，这里它接受迭代的每个项的值。 

## 局部变量

`NgFor`还提供了可以被局部变量别名的其他值：

* index - 当前项目的位置在从0开始的迭代中
* first - 如果当前项是可迭代中的第一个项，则为true
* last - 如果当前项是可迭代中的最后一个项，则为true
* even - 如果当前索引为偶数，则为true
* odd - 如果当前索引是奇数，则为true


```typescript
@Component({
  selector: 'app',
  template: `
    <for-example
      *ngFor="let episode of episodes; let i = index; let isOdd = odd"
      [episode]="episode"
      [ngClass]="{ odd: isOdd }">
      {{i+1}}. {{episode.title}}
    </for-example>
    <hr/>
    <h2>Desugared</h2>
    <template ngFor [ngForOf]="episodes" let-episode let-i="index" let-isOdd="odd">
      <for-example [episode]="episode" [ngClass]="{ odd: isOdd }">
        {{i+1}}. {{episode.title}}
      </for-example>
    </template>
  `
})
```
[View Example](https://plnkr.co/edit/58A5p8cWpVIY7Ne4O7aO?p=preview)

##trackBy

通常`ngFor`用于迭代具有唯一ID字段的对象列表。 在这种情况下，我们可以提供一个`trackBy`函数，帮助Angular跟踪列表中的项目，以便它可以检测哪些项目已添加或删除，并提高性能。

Angular 2将通过引用来尝试和跟踪对象，以确定应该创建和销毁哪些项目。 然而，如果你用一个新的对象源代替列表，也许是一个API请求的结果，我们可以通过告诉Angular 2如何跟踪事情来获得一些额外的性能。

例如，如果`Add Episode`按钮是要发出请求并返回新的剧集列表，我们可能不想销毁并重新创建列表中的每个项目。 如果剧集有唯一的ID，我们可以添加一个`trackBy`函数：

```typescript
@Component({
  selector: 'app-root',
  template: `
  <button
    (click)="addOtherEpisode()"
    [disabled]="otherEpisodes.length === 0">
    Add Episode
  </button>
  <app-for-example
    *ngFor="let episode of episodes;
    let i = index; let isOdd = odd;
    trackBy: trackById" [episode]="episode"
    [ngClass]="{ odd: isOdd }">
    {{episode.title}}
  </app-for-example>
  `
})
export class AppComponent {

  otherEpisodes = [
    { title: 'Two Swords', director: 'D. B. Weiss', id: 8 },
    { title: 'The Lion and the Rose', director: 'Alex Graves', id: 9 },
    { title: 'Breaker of Chains', director: 'Michelle MacLaren', id: 10 },
    { title: 'Oathkeeper', director: 'Michelle MacLaren', id: 11 }]

  episodes = [
    { title: 'Winter Is Coming', director: 'Tim Van Patten', id: 0 },
    { title: 'The Kingsroad', director: 'Tim Van Patten', id: 1 },
    { title: 'Lord Snow', director: 'Brian Kirk', id: 2 },
    { title: 'Cripples, Bastards, and Broken Things', director: 'Brian Kirk', id: 3 },
    { title: 'The Wolf and the Lion', director: 'Brian Kirk', id: 4 },
    { title: 'A Golden Crown', director: 'Daniel Minahan', id: 5 },
    { title: 'You Win or You Die', director: 'Daniel Minahan', id: 6 }
    { title: 'The Pointy End', director: 'Daniel Minahan', id: 7 }
  ];

  addOtherEpisode() {
    // We want to create a new object reference for sake of example
    let episodesCopy = JSON.parse(JSON.stringify(this.episodes))
    this.episodes=[...episodesCopy,this.otherEpisodes.pop()];
  }
  trackById(index: number, episode: any): number {
    return episode.id;
  }
}
```
要了解这会如何影响`ForExample`组件，我们向它添加一些 console。
```typescript
export class ForExampleComponent {
  @Input() episode;

  ngOnInit() {
    console.log('component created', this.episode)
  }
  ngOnDestroy() {
    console.log('destroying component', this.episode)
  }
}
```
[View Example](https://plnkr.co/edit/jQmozF?p=preview)

当我们查看示例时，当我们点击`Add Episode`时，我们可以看到控制台输出，指示只有一个组件被创建 - 用于新添加到列表中的项目。

但是，如果我们从`*ngFor`中删除`trackBy` - 每次我们单击按钮，我们将看到组件中的项目被销毁和重新创建。

[View Example Without trackBy](https://plnkr.co/edit/hC2cIK?p=preview)