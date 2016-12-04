# 验证模板驱动的表单

### 验证

验证模板驱动的表单，下面是使用HTML5的做法：

```html
<!-- a required field -->
<input type="text" required>

<!-- an optional field of a specific length -->
<input type="text" pattern=".{3,8}">

<!-- a non-optional field of specific length -->
<input type="text" pattern=".{3,8}" required>

<!-- alphanumeric field of specific length -->
<input type="text" pattern="[A-Za-z0-9]{0,5}">
```

请注意，`pattern`属性是JavaScript RegEx语法的一个不太强大的版本。
还有其他HTML5属性，可以学习和应用于各种类型的输入; 然而在大多数情况下，它们作为上限和下限，防止添加或删除额外信息。

```html
<!-- a field which will accept no more than 5 characters -->
<input type="text" maxlength="5">
```

在编写模板驱动表单时，可以使用这两种方法之一。 关注用户体验：在某些情况下，防止意外输入是有意义的，在其他情况下，允许不受限制的输入是有意义的，但提供类似计数器的东西以显示限制。