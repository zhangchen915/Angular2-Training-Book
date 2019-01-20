# 使用JiT Compiler将翻译文件导入到应用程序中

JiT（Just-in-time）编译器在应用程序加载时动态编译应用程序。要做到这一点，我们将需要依靠3个提供者告诉JiT编译器如何翻译特定语言的模板文本：

- `TRANSLATIONS`是包含翻译文件内容的字符串。
- `TRANSLATIONS_FORMAT` 是文件的格式。
- `LOCALE_ID` 是目标语言的区域设置。

以下是如何使用法语的翻译提供商来翻译应用程序。我们假设翻译文件是`messages.fr.xlf`。

*app/index.ts*:

```typescript
import { NgModule, TRANSLATIONS, TRANSLATIONS_FORMAT, LOCALE_ID } from '@angular/core';
import { BrowserModule } from '@angular/platform-browser';
import { platformBrowserDynamic } from '@angular/platform-browser-dynamic';
import { Hello } from './app.component.ts';

// Using SystemJs' text plugin
import translations from './messages.fr.xlf!text';
const localeId = 'fr';

@NgModule({
  imports: [
    BrowserModule
  ],
  declarations: [
    Hello
  ],
  bootstrap: [ Hello ]
})
export class AppModule {
}

platformBrowserDynamic().bootstrapModule(AppModule, {
  providers: [
    { provide: TRANSLATIONS, useValue: translations },
    { provide: TRANSLATIONS_FORMAT, useValue: 'xlf' },
    { provide: LOCALE_ID, useValue: localeId }
  ]
});
```

[View Example](http://plnkr.co/edit/p1bK6TFnKupReH9HmCOt?p=preview)

我们使用SystemJS文本插件来导入原始xlf文件。 我们可以使用webpack和`raw-loader`来实现相同的效果。 更好的是，我们可以基于我们感兴趣的语言进行http调用，并在应用程序加载后异步引导应用程序。