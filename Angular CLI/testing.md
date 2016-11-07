#测试
CLI生成的应用程序集成自动化测试。 CLI通过使用Karma测试运行器来执行此操作。
##单元测试
要执行单元测试，请运行ng测试。 这将运行所有由Karma配置文件在`config / karma.conf.js`匹配的测试。 它设置为匹配默认情况下以`.spec.ts`结尾的所有TypeScript文件。
##端到端(e2e)测试
可以通过运行`ng e2e`来执行端到端测试。 在执行端到端测试之前，应用程序必须在某个地址提供服务。Angular CLI使用量角器。 默认情况下，它将尝试访问`localhost：4200`; 如果正在使用另一个端口，则必须更新位于`config / protractor.conf.js`的配置设置。