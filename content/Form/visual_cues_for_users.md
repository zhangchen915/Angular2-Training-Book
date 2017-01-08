# 用户的视觉提示

HTML5为其输入字段提供`:invalid`和`:valid`伪选择器。

```css
input[type="text"]:valid {
  border: 2px solid green;
}

input[type="text"]:invalid {
  border: 2px solid red;
}
```

不幸的是，这个系统是作用有限，并且将需要更多的操作，才能处理复杂的形式或用户行为。

Angular 2不是编写额外的代码，而是创建和实施自己的CSS类，以管理这些行为，为您提供了几个样式类，已经可以在inputs上使用。

```css
/* field value is valid */
.ng-valid {}

/* field value is invalid */
.ng-invalid {}

/* field has not been clicked in, tapped on, or tabbed over */
.ng-untouched {}

/* field has been previously entered */
.ng-touched {}

/* field value is unchanged from the default value */
.ng-pristine {}

/* field value has been modified from the default */
.ng-dirty {}
```

记住三对状态：

- valid / invalid
- untouched / touched
- pristine / dirty

这些对可以在您的CSS中使用许多组合，以根据它们代表的三个独立的标志来改变样式。 当输入的状态改变时，Angular将在每个输入上的对之间切换。