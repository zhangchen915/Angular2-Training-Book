# 动画

Angular动画是基于标准的Web动画API([Web Animations API](https://w3c.github.io/web-animations/))构建的。但这个IE和safari都不支持，所以我们要向引入兼容模块。

```js
import { BrowserModule } from '@angular/platform-browser';
import { BrowserAnimationsModule } from '@angular/platform-browser/animations';
```

这些API被设计成 [CSS Animations](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_Animations) and [CSS Transitions](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_Transitions)的接口，所以和CSS效果基本一致。

#### 导入

```js
import {
  trigger, // 动画封装触发，外部的触发器
  state, // 转场状态控制
  style,transition,animate,keyframes
} from '@angular/animations';
```



是通过状态的转换完成的。

####auto属性计算

有些元素直到运行时才会知道维样式属性的值。这种情况用CSS很难解决，所以angular提供了`*`属性值。他会在运行时计算属性的值，然后插入动画。

```Js
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

