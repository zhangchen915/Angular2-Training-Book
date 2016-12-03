# **Why Angular 2?**

如今有很多前端JavaScript框架可以选择，每个都有他自己的一套权衡。 很多人对 Angular 1.x 给予他们的功能很高兴 。. Angular 2  改进了这些功能，并使其更快，更可扩展性和更现代。  在 Angular 1.x 中发现价值的企业将在 Angular 2 中找到更多价值。

## **Angular 2's 优势**

The first release of Angular provided programmers with the tools to develop and architect large scale JavaScript applications, but its age has revealed a number of flaws and sharp edges. Angular 2 was built on five years of community feedback.

### **Angular 2 更简单**

新角2的代码库是更现代，更能干并且比 Angular 1.x 更容易让新人上手，同时也更容易为项目退伍军人一起工作。

使用 Angular 1，开发者必须了解 Controllers, Services, Factories, Providers 和其他概念，可能会造成混乱，特别是对新程序员。

Angular 2 is a more streamlined framework that allows programmers to focus on simply building JavaScript classes. Views and controllers are replaced with components, which can be described as a refined version of directives. Even experienced Angular programmers are not always aware of all the capabilities of Angular 1.x directives. Angular 2 components are considerably easier to read, and their API features less jargon than Angular 1.x's directives. Additionally, to help ease the transition to Angular 2, the Angular team has added a `.component` method to Angular 1.5, which has been [back-ported by community member Todd Motto to v1.3](https://toddmotto.com/angular-component-method-back-ported-to-1.3/).

### **TypeScript**

Angular 2 是用 TypeScript 写的，一个实现了许多ES6+新特性的JavaScript超集。 

通过专注于制造更容易为计算机处理的框架，Angular 2 允许更丰富的开发生态系统。使用先进的文本编辑器（或IDE）的程序员会注意到与自动完成和类型提示的显着改善。这些改进有助于降低学习 Angular 2 的认知负担。 Fortunately for traditional ES5 JavaScript programmers this does _not_ mean that development must be done in TypeScript or ES2015: programmers can still write vanilla JavaScript that runs without transpilation.

### **Familiarity**

尽管是一个完整的重写，Angular 2保留了Angular 1.x的许多核心概念和约定，例如。 一个简化的，“本地JS”依赖注入的实现。 这意味着已经熟练使用Angular的程序员将比另一个类似React或类似Ember的框架更容易迁移到Angular 2。


### **Performance and Mobile**

Angular 2 was designed for mobile from the ground up. Aside from limited processing power, mobile devices have other features and limitations that separate them from traditional computers. Touch interfaces, limited screen real estate and mobile hardware have all been considered in Angular 2.

Desktop computers will also see dramatic improvements in performance and responsiveness.

Angular 2, like React and other modern frameworks, can leverage performance gains by rendering HTML on the server or even in a web worker. Depending on application\/site design this isomorphic rendering can make a user's experience _feel_ even more instantaneous.

The quest for performance does not end with pre-rendering. Angular 2 makes itself portable to native mobile by integrating with [NativeScript](https://www.nativescript.org/), an open source library that bridges JavaScript and mobile. Additionally, the Ionic team is working on an Angular 2 version of their product, providing _another_ way to leverage native device features with Angular 2.

### **Project Architecture and Maintenance**

The first iteration of Angular provided web programmers with a highly flexible framework for developing applications. This was a dramatic shift for many web programmers, and while that framework was helpful, it became evident that it was often too flexible. Over time, best practices evolved, and a community-driven structure was endorsed.

Angular 1.x tried to work around various browser limitations related to JavaScript. This was done by introducing a module system that made use of dependency injection. This system was novel, but unfortunately had issues with tooling, notably minification and static analysis.

Angular 2.x makes use of the ES2015 module system, and modern packaging tools like webpack or SystemJS. Modules are far less coupled to the "Angular way", and it's easier to write more generic JavaScript and plug it into Angular. The removal of minification workarounds and the addition of rigid prescriptions make maintaining existing applications simpler. The new module system also makes it easier to develop effective tooling that can reason better about larger projects.

### **New Features**

Angular 2 中的一些新特性：

* Form Builder
* Change Detection
* Templating
* Routing
* Annotations
* Observables
* Shadow DOM

## **Differences Between Angular 1 & 2**

> Note that "Transitional Architecture" refers to a style of Angular 1 application written in a way that mimics Angular 2's component style, but with controllers and directives instead of TypeScript classes.

|  |Old School Angular 1.x  |  Angular 1.x Best Practices|Transitional Architecture  | Angular 2 |
| --- | --- | --- | --- | --- |
| Nested scopes \("$scope", watches\) | Used heavily | Avoided | **Avoided** | Gone |
| Directives vs controllers | Use as alternatives | Used together | **Directives as components** | Component directives |
| Controller and service implementation | Functions | Functions | **ES6 classes** | ES6 classes |
| Module system | Angular's modules | Angular's modules | **ES6 modules** | ES6 modules |
| Transpiler required | No | No | **TypeScript** | TypeScript |

