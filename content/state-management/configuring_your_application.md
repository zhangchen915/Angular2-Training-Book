# 配置你的应用

一旦创建了reducer，就可以配置Angular应用程序了。 在您应用的主模块中，简单的将`StoreModule.provideStore()` 调用添加到`@NgModule`的导入中：

*app/app.module.ts*

```typescript
import {BrowserModule} from '@angular/platform-browser';
import {NgModule} from '@angular/core';
import {FormsModule} from '@angular/forms';
import {HttpModule} from '@angular/http';
import {StoreModule} from '@ngrx/store';
import {EffectsModule} from '@ngrx/effects';

import 'rxjs/Rx';

import {rootReducer} from './store/rootReducer';
import {CounterActions} from './store/actions';
import {CounterEffects} from './store/effects';
import {AppComponent, CounterComponent} from './components';
import {CounterService} from './services';

@NgModule({
  imports: [
    BrowserModule,
    FormsModule,
    HttpModule,
    StoreModule.provideStore(rootReducer)
  ],
  declarations: [
    AppComponent,
    CounterComponent
  ],
  providers: [
    CounterActions,
    CounterService
  ],
  bootstrap: [AppComponent]
})
export class AppModule {

}
```