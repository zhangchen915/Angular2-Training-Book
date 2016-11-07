#集成现有应用程序
不是用CLI创建的应用程序可以集成到以后使用CLI。 这是通过转到现有的应用程序的文件夹和运行`ng init`。
由于现有应用程序的文件夹结构可能与CLI创建的文件夹结构不同，因此init命令具有一些配置选项。

* `--source-dir`标识源文件的相对路径（default = src）
* `--prefix`标识Angular 2应用程序文件所在的源目录中的路径（default = app）
* `--style`标识其他样式文件所在的路径（default = css）。