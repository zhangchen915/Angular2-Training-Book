# Angular: Why TypeScript?

![](assets/ts.png)

Angular 2 is written in TypeScript. In this article I will talk about why we made the decision. I’ll also share my experience of working with TypeScript: how it affects the way I write and refactor my code.

#### I like TypeScript, but you don’t have to

Even though Angular 2 is written in TypeScript, you don’t have to use it to write Angular 2 applications. The framework also works great with ES5, ES6, and Dart.

#### TypeScript Has Great Tools

The biggest selling point of TypeScript is tooling. **It provides advanced autocompletion, navigation, and refactoring. Having such tools is almost a requirement for large projects. **Without them the fear changing the code puts the code base in a semi-read-only state, and makes large-scale refactorings very risky and costly.

TypeScript is not the only typed language that compiles to JavaScript. There are other languages with stronger type systems that in theory can provide absolutely phenomenal tooling. But in practice most of them do not have anything other than a compiler. **This is because building rich dev tools has to be an explicit goal from day one, which it has been for the TypeScript team. **That is why they built language services that can be used by editors to provide type checking and autocompletion. If you have wondered why there are so many editors with great TypeScript supports, the answer is the language services.

The fact that intellisense and basic refactorings (e.g., rename a symbol) are reliable makes a huge impact on the process of writing and especially refactoring code. Although it is hard to measure, I feel that the refactorings that would have taken a few days before now can be done in less than a day.

While TypeScript greatly improves the code editing experience, it makes the dev setup more complex, especially comparing to dropping an ES5 script on a page. In addition, you cannot use tools analyzing JavaScript source code (e.g., JSHint), but there are usually adequate replacements.

#### TypeScript is a Superset of JavaScript

Since TypeScript is a superset of JavaScript, you don’t need to go through a big rewrite to migrate to it. You can do it gradually, one module at a time.

Just pick a module, rename the .js files into .ts, then incrementally add type annotations. When you are done with this module, pick the next one. Once the whole code base is typed, you can start tweaking the compiler settings to make it more strict.

This process can take some time, but it was not a big problem for Angular 2, when we were migrating to TypeScript. Doing it gradually allowed us to keep developing new functionality and fixing bugs during the transition.

#### TypeScript Makes Abstractions Explicit

**A good design is all about well-defined interfaces. And it is much easier to express the idea of an interface in a language that supports them.**

For instance, imagine a book-selling application where a purchase can be made by either a registered user through the UI or by an external system through some sort of an API.

![img](https://cdn-images-1.medium.com/max/800/0*47m053Nw8olCUOEi.png)

As you can see, both classes play the role of a purchaser. Despite being extremely important for the application, the notion of a purchaser is not clearly expressed in the code. There is no file named purchaser.js. And as a result, it is possible for someone modifying the code to miss the fact that this role even exists.

It is hard, just by looking at the code to tell what objects can play the role of a purchaser, and what methods this role has. We do not know for sure, and we will not get much help from our tools. We have to infer this information manually, which is slow and error-prone.

Now, compare it with a version where we explicitly define the Purchaser interface.

The typed version clearly states that we have the Purchaser interface, and the User and ExternalSystem classes implement it. So TypeScript interfaces allow us to define abstractions/protocols/roles.

**It is important to realize that TypeScript does not force us to introduce extra abstractions.** The Purchaser abstraction is present in the JavaScript version of the code, but it is not explicitly defined.

**In a statically-typed language, boundaries between subsystems are defined using interfaces. Since JavaScript lacks interfaces, boundaries are not well expressed in plain JavaScript. Not being able to clearly see the boundaries, developers start depending on concrete types instead of abstract interfaces, which leads to tight coupling.**

My experience of working on Angular 2 before and after our transition to TypeScript reinforced this belief. Defining an interface forces me to think about the API boundaries, helps me define the public interfaces of subsystems, and exposes incidental coupling.

#### TypeScript Makes Code Easier to Read and Understand

Yes, I know it does not seem intuitive. Let me try to illustrate what I mean with an example. Let’s look at this function jQuery.ajax(). What kind of information can we get from its signature?

The only thing we can tell for sure is that the function takes two arguments. We can guess the types. Maybe the first one is a string and the second one is a configuration object. But it is just a guess, and we might be wrong. We have no idea what options go into the settings object (neither their names nor their types), or what this function returns.

There is no way we can call this function without checking the source code or the documentation. Checking the source code is not a good option — the point of having functions and classes is to be able to use them without knowing how they are implemented. In other words, we should rely on their interfaces, not on their implementation. We can check the documentation, but it is not the best developer experience — it takes additional time, and the docs are often out-of-date.

So although it is easy to read jQuery.ajax(url, settings), to really understand how to call this function we need to either read its implementation or its docs.

Now, contrast it with a typed version.

It gives us a lot more information.

- The first argument of this function is a string.
- The settings argument is optional. We can see all the options that can be passed into the function, and not only their names, but also their types.
- The function returns a JQueryXHR object, and we can see its properties and functions.

The typed signature is certainly longer than the untyped one, but :string, :JQueryAjaxSettings, and JQueryXHR are not clutter. They are important documentation that improves the comprehensibility of the code. We can understand the code to a much greater degree without having to dive into the implementation or reading the docs. My personal experience is that I can read the typed code faster because types provide more context to understand the code. But if any of the readers can find a study on how types affect code readability, please leave a comment.

One thing that is different about TypeScript comparing to many other languages compiled to JavaScript is that its type annotations are optional, and jQuery.ajax(url, settings) is still valid TypeScript. So instead of an on-off switch, TypeScript’s types are more of a dial. If you find that the code is trivial to read and understand without type annotations, do not use them. **Use types only when they add value.**

#### Does TypeScript Limit Expressiveness?

Dynamically-typed languages have inferior tooling, but they are more malleable and expressive. I think using TypeScript makes your code more rigid, but to a much lesser degree than people think. Let me show you what I mean. Let’s say I use ImmutableJS to define the Person record.

How do we type the record? Let’s start with defining an interface called Person:

If we try to do the following:

the TypeScript compiler will complain. It does not know that PersonRecord is actually compatible with Person because PersonRecord is created reflectively. Some of you with the FP background are probably saying: “If only TypeScript had dependent types!” But it does not. TypeScript’s type system is not the most advanced one. But its goal is different. It is not here to prove that the program is 100% correct. It is about giving you more information and enable greater tooling. So it is OK to take shortcuts when the type system is not flexible enough. So we can just cast the created record, as follows:

The typed example:

The reason why it works is because the type system is structural. As long as the created object has the right fields — name and age — we are good.

You need to embrace the mindset that it is OK to take shortcuts when working with TypeScript. Only then you will find using the language enjoyable. For instance, don’t try to add types to some funky metaprogramming code — most likely you won’t be able to express it statically. Type everything around that code, and tell the typechecker to ignore the funky bit. In this case you will not lose a lot of expressiveness, and the bulk of your code will remain toolable and analyzable.

This is similar to trying to get 100% unit test code coverage. Whereas getting 95% is usually not that difficult, getting 100% can be challenging, and may negatively affect the architecture of your application.

**The optional type system also preserves the JavaScript development workflow. Large parts of your application’s code base can be “broken”, but you can still run it. TypeScript will keep generating JavaScript, even when the type checker complains. This is extremely useful during development.**

#### Why TypeScript?

There are a lot of options available to frontend devs today: ES5, ES6 (Babel), TypeScript, Dart, PureScript, Elm, etc.. So why TypeScript?

Let’s start with ES5. ES5 has one significant advantage over TypeScript: it does not require a transpiler. This allows you to keep your build setup simple. You do not need to set up file watchers, transpile code, generate source maps. It just works.

ES6 requires a transpiler, so the build setup will not be much different from TypeScript. But it is a standard, which means that every single editor and build tool either supports ES6 or will support it. This is a weaker argument that it used to be as most editors at this point have excellent TypeScript support.

Elm and PureScript are elegant languages with powerful type systems that can prove a lot more about your program than TypeScript can. The code written in Elm and PureScript can be a lot terser than similar code written in ES5.

Each of these options has pros and cons, but I think TypeScript is in a sweet spot that makes it a great choice for most projects. **TypeScript takes 95% of the usefulness of a good statically-typed language and brings it to the JavaScript ecosystem. You still feel like you write ES6: you keep using the same standard library, same third-party libraries, same idioms, and many of the same tools (e.g., Chrome dev tools). It gives you a lot without forcing you out of the JavaScript ecosystem.**