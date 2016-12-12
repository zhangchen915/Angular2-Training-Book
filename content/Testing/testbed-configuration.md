# TestBed 配置（可选）

正如你将在测试组件中看到的，真实的组件测试通常依赖于Angular2测试实用程序`TestBed`，它需要一些配置。 最重要的是，我们需要使用`TestBed.initTestEnvironment`创建一个测试平台，然后才能使用TestBed进行单元测试。 在每次单元测试之前，必须根据需要创建，销毁和重置此测试环境。
在angular2-redux-starter中，此配置在`tests.configure.ts`文件中完成，并导入到每个单元测试中以便重新使用。

```typescript
import {
  getTestBed,
  ComponentFixtureAutoDetect,
  TestBed,
} from '@angular/core/testing';

import {
  BrowserDynamicTestingModule,
  platformBrowserDynamicTesting,
} from '@angular/platform-browser-dynamic/testing';

export const configureTests = (configure: (testBed: TestBed) => void) => {
  const testBed = getTestBed();

  if (testBed.platform == null) {
    testBed.initTestEnvironment(
      BrowserDynamicTestingModule,
      platformBrowserDynamicTesting());
  }

  testBed.configureCompiler({
      providers: [
        {provide: ComponentFixtureAutoDetect, useValue: true},
      ]
    });

  configure(testBed);

  return testBed.compileComponents().then(() => testBed);
};
```

`tests.configure.ts`创建测试平台（如果测试平台不存在），编译模板，然后导出`configureTests，`然后在我们的单元测试中导入和使用它。
下面来看看如何使用它：

```typescript
import { TestBed } from '@angular/core/testing';
import { ExampleComponent } from './index';
import { configureTests } from '../../tests.configure';
import { AppModule } from '../../modules/app.module';

describe('Component: Example', () => {
  let fixture;

  beforeEach(done => {
    const configure = (testBed: TestBed) => {
      testBed.configureTestingModule({
        imports: [AppModule],
      });
    };

    configureTests(configure).then(testBed => {
      fixture = testBed.createComponent(ExampleComponent);
      fixture.detectChanges();
      done();
    });
  });
```