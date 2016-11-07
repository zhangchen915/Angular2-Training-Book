#创建其他
CLI可以使用generate命令支持其他Angular 2实体，如服务，管道和指令。
ng generate [entity] [entity-name]
这将在src / app / [entity-name]。[entity] .ts和spec文件创建实体，或者在当前路径下创建实体，如果命令在项目的子文件夹中执行的话。 CLI为下列实体提供了开箱即用的蓝图：

Item	Command	Files generated
Component:	ng g component [name]	component, HTML, CSS, test spec files
Directive:	ng g directive [name]	component, test spec files
Pipe:	ng g pipe [name]	component, test spec files
Service:	ng g service [name]	component, test spec files
Class:	ng g class [name]	component, test spec files
Route:	ng g route [name]	component, HTML, CSS, test spec files (in new folder)
