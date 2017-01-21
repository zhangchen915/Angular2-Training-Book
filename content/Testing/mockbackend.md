# 使用MockBackend测试HTTP请求

要对我们的服务进行单元测试，我们不想发出实际的HTTP请求。 为了实现这一点，我们需要模拟我们的HTTP服务。 Angular 2为我们提供了一个`MockBackend`类，它可以被配置为提供对我们的请求的模拟响应，而不实际做出网络请求。
然后可以将配置的`MockBackend`注入到HTTP中，因此对服务的任何调用（例如`http.get`）将返回我们预期的数据，从而允许我们隔离真实网络流量测试服务。

*wikisearch.spec.ts*

```typescript
import {
  fakeAsync,
  inject,
  TestBed
} from '@angular/core/testing';
import {
  HttpModule,
  XHRBackend,
  ResponseOptions,
  Response,
  RequestMethod
} from '@angular/http';
import {
  MockBackend,
  MockConnection
} from '@angular/http/testing/mock_backend';

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

  beforeEach(() => {
    TestBed.configureTestingModule({
      imports: [HttpModule],
      providers: [
        {
          provide: XHRBackend,
          useClass: MockBackend
        },
        SearchWiki
      ]
    });
  });

  it('should get search results', fakeAsync(
    inject([
      XHRBackend,
      SearchWiki
    ], (mockBackend: XHRBackend, searchWiki: SearchWiki) => {

      const expectedUrl = 'https://en.wikipedia.org/w/api.php?' +
        'action=query&list=search&srsearch=Angular';

      mockBackend.connections.subscribe(
        (connection: MockConnection) => {
          expect(connection.request.method).toBe(RequestMethod.Get);
          expect(connection.request.url).toBe(expectedUrl);

          connection.mockRespond(new Response(
            new ResponseOptions({ body: mockResponse })
          ));
        });

      searchWiki.search('Angular')
        .subscribe(res => {
          expect(res).toEqual(mockResponse);
        });
    })
  ));

  it('should set foo with a 1s delay', fakeAsync(
    inject([SearchWiki], (searchWiki: SearchWiki) => {
      searchWiki.setFoo('food');
      tick(1000);
      expect(searchWiki.foo).toEqual('food');
    })
  ));

});
```

[View Example](http://plnkr.co/edit/K9gzDOcEOcmfFaOacdKZ?p=preview)

我们使用`inject`注入`SearchWiki`服务和`MockBackend`到我们的测试。然后，我们使用对`fakeAsync`的调用封装我们的整个测试，这将用于控制`SearchWiki`服务的异步行为以进行测试。

接下来，我们订阅来自我们后端的任何传入连接。这使我们可以访问一个对象`MockConnection` ，它允许我们配置我们想要从我们的后端发送的响应，以及测试任何来自我们正在测试的服务的请求。

在我们的示例中，我们要验证`SearchWiki`的搜索方法是否向正确的URL发出GET请求。这是通过查看当我们的`SearchWiki`服务连接到我们的模拟后端时获得的请求对象来完成。分析`request.url`属性，我们可以看到它的值是否是我们期望的值。这里我们只检查URL，但在其他情况下，我们可以看到是否已设置某些头，或者是否已发送某些POST数据。

现在，使用`MockConnection`对象，我们模拟了一些任意数据。我们创建一个新的`ResponseOptions`对象，我们可以配置我们的响应的属性。这遵循常规Angular 2响应类的格式。在这里，我们只需将body属性设置为您可能从维基百科中看到的基本搜索结果集。我们还可以设置Cookie，HTTP标头等等，或将状态值设置为非200状态，以测试我们的服务如何响应错误。一旦我们配置了`ResponseOptions，`我们创建一个Respond对象的新实例，并告诉我们后端通过调用`.mockRespond`开始将其用作响应。

可以使用多个响应。假设您的服务有两个可能的GET请求 - 一个用于`/api/users`，另一个用于`/api/users/1`。这些请求中的每一个具有不同的对应的模拟数据集合。当通过`MockBackend`订阅接收新的连接时，您可以检查请求的是什么类型的URL，并使用任何一组模拟数据进行响应。

最后，我们可以通过调用和订阅结果来测试`SearchWiki`服务的搜索方法。一旦我们的搜索过程完成，我们检查结果对象，看看它是否包含相同的数据，我们mock到我们的后端。如果是，那么恭喜你，你的测试已经通过。

在应该设置foo与1s延迟测试，你会注意到，我们调用`tick(1000)`模拟1秒的延迟。