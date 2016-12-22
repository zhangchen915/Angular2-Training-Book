# 测试HTTP请求

服务，由于它们执行异步任务的性质。 当我们进行HTTP请求时，我们使用异步方式，以免阻止应用程序的其余部分执行其操作。我们之前了解了一些组件的异步测试，幸运的是，很多这方面的知识也带到了测试异步服务中。

测试这种服务的基本策略是验证请求的内容（正确的URL），并确保我们模拟到服务中的数据通过正确的方法正确返回。

让我们来看一些代码：

*wikisearch.ts*

```
import {Http} from '@angular/http';
import {Injectable} from '@angular/core';
import {Observable} from 'rxjs';
import 'rxjs/add/operator/map'

@Injectable()
export class SearchWiki {
  constructor (private http: Http) {}

  search(term: string): Observable<any> {
    return this.http.get(
      'https://en.wikipedia.org/w/api.php?' +
      'action=query&list=search&srsearch=' + term
    ).map((response) => response.json());
  }

  searchXML(term: string): Observable<any> {
    return this.http.get(
      'https://en.wikipedia.org/w/api.php?' +
      'action=query&list=search&format=xmlfm&srsearch=' + term
    );
  }
}

```

这里是一个基本的服务。 它将使用搜索项查询维基百科，并返回一个带有查询结果的Observable。 搜索功能将使用提供的术语进行GET请求，searchXML方法将执行相同的操作，但请求响应为XML而不是JSON。 正如你可以看到，它取决于HTTP模块向wikipedia.org发出请求。

我们的测试策略是检查服务是否已请求正确的网址，一旦我们回复了模拟数据，我们就要验证它是否返回相同的数据。