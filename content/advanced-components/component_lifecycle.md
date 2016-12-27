# 组件生命周期

组件具有由Angular本身管理的生命周期。 Angular管理创建，渲染，数据绑定属性等。它还提供了钩子，允许我们响应关键的生命周期事件。

这里是完整的生命周期钩子接口清单：

- `ngOnChanges` - 当输入绑定值更改时调用
- `ngOnInit` - 第一次`ngOnChanges`之后
- `ngDoCheck` -  每次运行变化检测后
- `ngAfterContentInit` - 组件内容初始化之后
- `ngAfterContentChecked` - 在每次检查组件内容后
- `ngAfterViewInit` - 组件的视图被初始化之后
- `ngAfterViewChecked` - 在每次检查组件视图后
- `ngOnDestroy` - 只在组件被销毁之前

📄 from [Component Lifecycle](https://angular.io/docs/ts/latest/guide/lifecycle-hooks.html)

[View Example](http://plnkr.co/edit/SUxXHr0SV4Fvx2zCiRlE?p=preview)