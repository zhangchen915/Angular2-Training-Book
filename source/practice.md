#最佳实践与填坑的积累

#### 1. 删除由Angular组件创建的Host HTML元素选择器

写一个`remove-host` 指令

```typescript
//remove the host of avatar to be rendered as svg
@Directive({
    selector: '[remove-host]'
})
class RemoveHost {
    constructor(private el: ElementRef) {
    }

    //wait for the component to render completely
    ngOnInit() {
        var nativeElement: HTMLElement = this.el.nativeElement,
            parentElement: HTMLElement = nativeElement.parentElement;
        // move all children out of the element
        while (nativeElement.firstChild) {
            parentElement.insertBefore(nativeElement.firstChild, nativeElement);
        }
        // remove the empty element(the host)
        parentElement.removeChild(nativeElement);
    }
}
```

使用方式：
`<avatar [name]="hero.name" remove-host></avatar>`

*来自[stackoverflow](http://stackoverflow.com/questions/34280475/remove-the-host-html-element-selectors-created-by-angular-component)*

#### 2.判断`<ng-content>`为空

Wrap `ng-content` in an HTML element like a `div` to get a local reference to it, then bind the `ngIf`expression to `ref.childNodes.length == 0`:

```
template: `<div #ref><ng-content></ng-content></div> 
           <span *ngIf="ref.childNodes.length == 0">
              Display this if ng-content is empty!
           </span>`
```

来自[stackoverflow](http://stackoverflow.com/questions/35107211/in-angular-2-how-to-check-whether-ng-content-is-empty)