#自定义管道
Angular 2允许创建你自己的管道：
```ts
import {Pipe, PipeTransform} from '@angular/core';

const FILE_SIZE_UNITS = ['B', 'KB', 'MB', 'GB', 'TB', 'PB', 'EB', 'ZB', 'YB'];
const FILE_SIZE_UNITS_LONG = ['Bytes', 'Kilobytes', 'Megabytes', 'Gigabytes', 'Pettabytes', 'Exabytes', 'Zettabytes', 'Yottabytes'];

@Pipe({
  name: 'formatFileSize'
})
export class FormatFileSizePipe implements PipeTransform {
  transform(sizeInBytes: number, longForm: boolean): string {
    const units = longForm
      ? FILE_SIZE_UNITS_LONG
      : FILE_SIZE_UNITS;
    let power = Math.round(Math.log(sizeInBytes)/Math.log(1024));
    power = Math.min(power, units.length - 1);
    const size = sizeInBytes / Math.pow(1024, power); // size in new units
    const formattedSize = Math.round(size * 100) / 100; // keep up to 2 decimals
    const unit = units[power];
    return `${formattedSize} ${unit}`;
  }
}
```
每个自定义管道实现必须：

* 使@Pipe装饰器包含具有name属性的管道元数据。 此值将用于在模板表达式中调用此管道。 它必须是有效的JavaScript标识符。
* 实现PipeTransform接口的transform方法。 此方法接受管道的值和任何类型的可变数量的参数，并返回一个变换的（“管道”）值。

模板中的每个以冒号分隔的参数按相同的顺序映射到一个方法参数。
```ts
import {Component} from '@angular/core';

@Component({
  selector: 'hello',
  template: `
    <div>
      <p *ngFor="let f of fileSizes">{{ f | formatFileSize }}</p>
      <p>{{ largeFileSize | formatFileSize:true }}</p>
    </div>`
})
export class Hello {
  fileSizes = [10, 100, 1000, 10000, 100000, 10000000, 10000000000];
  largeFileSize = Math.pow(10, 15)
}
```
[查看示例](http://plnkr.co/edit/hFLQ3qyukTet1h7rREYW?p=preview)