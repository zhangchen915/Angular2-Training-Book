#类型
精明的读者可能会想知道当TypeScript程序员需要与没有类型信息的JavaScript模块接口时会发生什么。 TypeScript将标记为* .d.ts的文件视为定义文件。 这些文件旨在使用TypeScript来描述由JavaScript库提供的接口。

有一些社区的人致力于为JavaScript项目创建类型。 还有一个叫做typings的实用程序（npm install --save-dev typings），可以用来管理第三方包。 （在TypeScript 2.0中已弃用）

在TypeScript 2.0中，用户可以直接从@types通过npm获取类型文件（例如，npm install --save @ types / lodash将安装lodash类型文件）。