# 使用AoT编译器导入翻译文件

要使AoT（Ahead of time）编译器国际化，您必须：

- 为每种语言预构建一个单独的应用程序包 
- 确定用户需要哪种语言 
- 服务适当的应用程序包

要预构建独立的应用程序，您必须确保拥有设置AoT所需的工具。有关如何执行此操作的详细信息，请参阅 [AoT 食谱](https://angular.io/docs/ts/latest/cookbook/aot-compiler.html)。

准备就绪后，使用`ngc` 命令为编译器提供以下3个选项：

- `--i18nFile`: 翻译文件的路径
- `--locale`:语言环境的名称
- `--i18nFormat`:本地化文件的格式

例如，法语语言命令看起来像这样：

```
./node_modules/.bin/ngc --i18nFile=./locale/messages.fr.xlf --locale=fr --i18nFormat=xlf
```