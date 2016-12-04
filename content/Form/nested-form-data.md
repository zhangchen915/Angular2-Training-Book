# 嵌套表单数据

如果你发现你在把一维的表单替换成嵌套的数据上遇到了麻烦，不用怕，Angular已经涵盖了简单和复杂的情况。

假设你要提交一个类似下面的数据：

```
{
  "contact": {
    "firstname": "Bob",
    "lastname": "McKenzie",
    "email": "BobAndDoug@GreatWhiteNorth.com",
    "phone": "555-TAKE-OFF"
  },
  "address": {
    "street": "123 Some St",
    "city": "Toronto",
    "region": "ON",
    "country": "CA",
    "code": "H0H 0H0"
  },
  "paymentCard": {
    "provider": "Credit Lending Company Inc",
    "cardholder": "Doug McKenzie",
    "number": "123 456 789 012",
    "verification": "321",
    "expiry": "2020-02"
  }
}

```

虽然表单是平面和一维的，但从它们构建的数据不是。将您已经提供的数据转换为所需的形状， 这会导致复杂的变换。
更糟的是，在可能在表单输入中遇到命名冲突的情况下，您可能会发现自己为了语义的目的而使用长而笨拙的名称。

```
<form >
  <fieldset>
    <legend>Contact</legend>

    <label for="contact_first-name">First Name</label>
    <input type="text" name="contact_first-name" id="contact_first-name">

    <label for="contact_last-name">Last Name</label>
    <input type="text" name="contact_last-name" id="contact_last-name">

    <label for="contact_email">Email</label>
    <input type="email" name="contact_email" id="contact_email">

    <label for="contact_phone">Phone</label>
    <input type="text" name="contact_phone" id="contact_phone">
  </fieldset>

  <!-- ... -->

</form>

```

表单处理程序必须将该数据转换为API期望的表单。 幸运的是，这是Angular 2有一个解决方案。

### `ngModelGroup`

当在Angular 2中构建模板驱动的表单时，我们可以依靠`ngModelGroup`指令来实现一个更干净的实现，这样Angular就会将表单字段转换为嵌套数据。

```html
<form #paymentForm="ngForm" (ngSubmit)="purchase(paymentForm)">
  <fieldset ngModelGroup="contact">
    <legend>Contact</legend>

    <label>
      First Name <input type="text" name="firstname" ngModel>
    </label>
    <label>
      Last Name <input type="text" name="lastname" ngModel>
    </label>
    <label>
      Email <input type="email" name="email" ngModel>
    </label>
    <label>
      Phone <input type="text" name="phone" ngModel>
    </label>
  </fieldset>

  <fieldset ngModelGroup="address">
    <!-- ... -->
  </fieldset>

  <fieldset ngModelGroup="paymentCard">
    <!-- ... -->
  </fieldset>
</form>
```

> - 使用替代HTML5标签格式; ID与`ngForm` / `ngModelparadigm`没有关系
> - 除了语义目的，ngModelGroup不必在`<fieldset>`上使用，它会和放在`<div>`上一样。

如果我们填写表单，它将以我们需要的API的形式结束，而且仍然可以依靠HTML进行验证。

