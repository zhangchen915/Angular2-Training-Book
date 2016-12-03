#将Streams与flatMap组合

flatMap案例：

假设我们想实现一个AJAX搜索功能，其中文本字段中的每个按键都将自动执行搜索并用结果更新页面。 这怎么样？ 好的，我们将有一个Observable订阅来自一个输入字段的事件，并且对输入的每个更改，我们要执行一些HTTP请求，这也是我们订阅的Observable。 我们最终得到的是一个Observable的Observable。

通过使用flatMap，我们可以将我们的事件流（文本字段上的keypress事件）转换为我们的响应流（来自HTTP请求的搜索结果）。
app/services/search.service.ts
```ts
import {Http} from '@angular/http';
import {Injectable} from '@angular/core';

@Injectable()
export class SearchService {

  constructor(private http: Http) {}

  search(term: string) {
    return this.http
            .get('https://api.spotify.com/v1/search?q=' + term + '&type=artist')
            .map((response) => response.json())
  }
}
```
这里我们有一个基本的服务，将通过执行获取请求与提供的搜索术语进行Spotify的搜索查询。 此搜索函数返回一个已经过一些基本后处理（将响应转换为JSON对象）的Observable。
好的，让我们来看看将使用此服务的组件。

app/app.component.ts
```ts
import { Component } from '@angular/core';
import { FormControl, FormGroup, FormBuilder } from '@angular/forms';
import { SearchService } from './services/search.service';
import 'rxjs/Rx';

@Component({
    selector: 'app',
    template: `
        <form [formGroup]="coolForm"><input formControlName="search" placeholder="Search Spotify artist"></form>

        <div *ngFor="let artist of result">
          {{artist.name}}
        </div>
    `
})

export class MyApp {
    searchField: FormControl;
    coolForm: FormGroup;

    constructor(private searchService:SearchService, private fb:FormBuilder) {
        this.searchField = new FormControl();
        this.coolForm = fb.group({search: this.searchField});

        this.searchField.valueChanges
          .debounceTime(400)
            .flatMap(term => this.searchService.search(term))
            .subscribe((result) => {
                this.result = result.artists.items
            });
    }
}
```
这里我们设置了一个基本表单，其中包含单个字段，搜索，我们为事件更改订阅。 我们还为来自SearchService的任何结果设置了一个简单的绑定。 这里的真正的魔力是flatMap，它允许我们把我们两个单独的订阅的Observables变成一个单一的粘性流，我们可以用来控制来自用户输入和服务器响应的事件。
注意，flatMap通过在“trunk”流上发射将在“分支”流上发射的一切，将观察者流（即Observable的观察者）平坦化为发射值流（简单的Observable）。