# 结构指令

结构指令是处理组件或元素通过使用模板标签呈现的方式。 这允许我们运行一些代码来决定最终渲染的输出。 Angular 2有一些内置的结构指令，如`ngIf`  ，`ngFor`和`ngSwitch` 。

*注意：对于不熟悉模板标记的用户，它是一个具有几个特殊属性的HTML元素。 嵌入在模板标记中的内容不会在网页加载时呈现，而是在运行时通过代码加载的内容。 有关模板标记的更多信息，请访问[MDN文档](https://developer.mozilla.org/en/docs/Web/HTML/Element/template)。*

结构化指令在模板中有自己的特殊语法，它们作为语法糖。

```
@Component({
  selector: 'app-directive-example',
  template: `
    <p *structuralDirective="expression">
      Under a structural directive.
    </p>
  `
})

```

我们的伪结构伪指令不带方括号，而是带有星号。 注意，即使没有方括号，绑定仍然是表达式绑定。 这是因为它是语法糖，允许以更直观的方式使用指令，类似于在Angular 1中使用指令。上面的组件模板等价于以下：

```typescript
@Component({
  selector: 'app-directive-example',
  template: `
    <template [structuralDirective]="expression">
      <p>
        Under a structural directive.
      </p>
    </template>
  `
})
```

这里，我们看到我们前面说的结构化指令使用的`template` 标签。 Angular 2也有一个做同样的事情的内置`template` 指令。

```typescript
@Component({
  selector: 'app-directive-example',
  template: `
    <p template="structuralDirective expression">
      Under a structural directive.
    </p>
  `
})
```