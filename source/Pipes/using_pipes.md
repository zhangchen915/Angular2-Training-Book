#使用管道
像过滤器一样，管道也将数据作为输入，并将其转换为所需的输出。使用管道的基本示例如下所示：
```typescript
import {Component} from '@angular/core';

@Component({
  selector: 'product-price',
  template: `<p>Total price of product is {{ price | currency }}</p>`
})
export class ProductPrice {
  price: number = 100.1234;
}
```
[查看示例](http://plnkr.co/edit/WH5RGHItodj1RHznlNHo?p=preview)
#传递参数
管道可以接受可选参数以修改输出。要将参数传递到管道，只需在管道表达式的末尾添加冒号和参数值：
```pipeName: parameterValue```
你也可以这样传递多个参数：
```pipeName: parameter1: parameter2```
```typescript
import {Component} from '@angular/core';

@Component({
    selector: 'product-price',
    template: '<p>Total price of product is {{ price | currency: "CAD": true: "1.2-4" }}</p>'
})
export class ProductPrice {
  price: number = 100.123456;
}
```
[View Example](http://plnkr.co/edit/IjGPii3n7qpezcglp03O?p=preview)

# 链接管道

链接管道

我们可以将多个管道连接在一起，以便在一个表达式中使用多个管道。
```typescript
import {Component} from '@angular/core';

@Component({
    selector: 'product-price',
    template: '<p>Total price of product is {{ price | currency: "CAD": true: "1.2-4" | lowercase }}</p>'
})
export class ProductPrice {
  price: number = 100.123456;
}
```
[View Example](http://plnkr.co/edit/mnnujN8qPMfRzmNg4uo4?p=preview)