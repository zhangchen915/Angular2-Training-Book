# 变化检测策略：OnPush

要通知Angular，我们将遵守前面提到的条件以提高性能，我们将在`MovieComponent`中使用`OnPush` 变化检测策略。

*app/movie.component.ts*

```
@Component({
  // ...
  changeDetection: ChangeDetectionStrategy.OnPush
}
export class MovieComponent {
  // ...
}
```

[View Example](http://plnkr.co/edit/yjr8R6LhWpOKcGnAwYNS?p=preview)

这将通知Angular我们的组件只取决于它的输入，并且传递给它的任何对象应该被认为是不可变的。这次当我们点击“Change Actor Properties”按钮时视图不会发生任何变化。

让我们再回顾一下它背后的逻辑。 当用户单击按钮时，调用方法`changeActorPropertiesis`并更新`actor`对象的属性。

当变化检测分析绑定到AppComponent模板的属性时，它将看到与之前相同的景象：

- Is `slogan !== previousSlogan` No, it's the same.
- Is `title !== previousTitle`? No, it's the same.
- Is `actor !== previousActor`? No, it's the same.

但是这一次，我们明确告诉Angular，我们的组件只取决于它的输入，并且它们都是不可变的。 然后，Angular假定`MovieComponent`没有改变，并且将跳过对该组件的检查。 因为我们没有强制`actor`对象是不可变的，所以我们的模型与视图不同步。

让我们重新运行应用程序，但这次我们将点击“Change Actor Object”按钮。 这次，我们创建一个`Actor`类的新实例，并将其分配给`this.actor`对象。 当变化检测分析与`AppComponent`模板绑定的属性时，它会发现：

- Is `slogan !== previousSlogan` No, it's the same.
- Is `title !== previousTitle`? No, it's the same.
- Is `actor !== previousActor`? **Yes**, it has changed.

因为变化检测现在知道`actor`对象改变了（它是一个新的实例），它将继续检查模板，以便`MovieComponent`更新其视图。 最后，我们的视图模版和数据模型是同步的。