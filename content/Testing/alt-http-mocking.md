# 替代HTTP模拟策略

使用`MockBackend`的一个替代方法是创建我们自己的轻模拟。 这里我们创建一个对象，然后告诉TypeScript使用类型断言将其视为`Http` 。 然后我们为它的get方法创建一个监测器，并返回一个类似于真正的Http服务的observable 。

此方法仍然允许我们检查服务是否已请求正确的URL，并返回预期的数据。

*wikisearch.spec.ts*

```typescript
import {
  fakeAsync,
  inject,
  TestBed
} from '@angular/core/testing';
import {
  HttpModule,
  Http,
  ResponseOptions,
  Response
} from '@angular/http';
import { Observable } from 'rxjs/Observable';
import 'rxjs/add/observable/of';
import {SearchWiki} from './wikisearch.service';

const mockResponse = {
  "batchcomplete": "",
  "continue": {
    "sroffset": 10,
    "continue": "-||"
  },
  "query": {
    "searchinfo": {
      "totalhits": 36853
    },
    "search": [{
      "ns": 0,
      "title": "Stuff",
      "snippet": "<span></span>",
      "size": 1906,
      "wordcount": 204,
      "timestamp": "2016-06-10T17:25:36Z"
    }]
  }
};

describe('Wikipedia search service', () => {
  let mockHttp: Http;

  beforeEach(() => {
    mockHttp = { get: null } as Http;

    spyOn(mockHttp, 'get').and.returnValue(Observable.of({
      json: () => mockResponse
    }));

    TestBed.configureTestingModule({
      imports: [HttpModule],
      providers: [
        {
          provide: Http,
          useValue: mockHttp
        },
        SearchWiki
      ]
    });
  });

  it('should get search results', fakeAsync(
    inject([SearchWiki], searchWiki => {
      const expectedUrl = 'https://en.wikipedia.org/w/api.php?' +
        'action=query&list=search&srsearch=Angular';

      searchWiki.search('Angular')
        .subscribe(res => {
          expect(mockHttp.get).toHaveBeenCalledWith(expectedUrl);
          expect(res).toEqual(mockResponse);
        });
    })
  ));

});
```

[View Example](http://plnkr.co/edit/eplM1SETfR51USVZLUlU?p=preview)