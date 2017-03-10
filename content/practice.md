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