# 动画

Angular动画是基于标准的Web动画API([Web Animations API](https://w3c.github.io/web-animations/))构建的。但这个IE和safari都不支持，所以我们要向引入兼容模块。

```js
import { BrowserModule } from '@angular/platform-browser';
import { BrowserAnimationsModule } from '@angular/platform-browser/animations';
```

这些API被设计成 [CSS Animations](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_Animations) 和 [CSS Transitions](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_Transitions)的接口，所以和CSS效果基本一致。

更多内容请参考官方文档：https://angular.cn/guide/animations

⚡️大多数动画都能用等效的css来实现，如果比较简单的动画并不推荐使用动画库。这里举个单独提一下auto属性值计算的例子，这些元素直到运行时才会知道属性的值。这种情况用CSS很难解决了，所以angular提供了`*`属性值。他会在运行时计算属性的值，然后插入动画。

```typescript
animations: [
  trigger('shrinkOut', [
    state('in', style({height: '*'})),
    transition('* => void', [
      style({height: '*'}),
      animate(250, style({height: 0}))
    ])
  ])
]
```

