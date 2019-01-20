# 入门

### Opt-In APIs

在我们深入了解任何表单的功能之前，我们需要使用`FormsModule`和/或`ReactiveFormsModule`引导我们的应用程序。

```typescript
import { platformBrowserDynamic } from '@angular/platform-browser-dynamic'
import { FormsModule } from '@angular/forms';
import { AppComponent } from './components'

@NgModule({
  imports: [
    BrowserModule,
    FormsModule,
  ],
  declarations: [AppComponent],
  bootstrap: [AppComponent]
})
export class AppModule {
}

platformBrowserDynamic().bootstrapModule(AppModule)
```

### Input 标签

大多数表单示例使用以下HTML5样式来标记Input：

```html
<label for="name">Name</label>
<input type="text" name="username" id="name">
```

Angular 2也支持替代HTML5风格，这排除了在`<input>` 上的`id`的必要性：

```html
<label>
  Name
  <input type="text" name="username">
</label>
```