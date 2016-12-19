## 使用 Angular i18n 工具

现在我们已经标记了我们的文本，让我们下载一个名为`ng-xi18n`的Angular CLI工具，它将提取此文本，并将其放入[XLIFF](https://en.wikipedia.org/wiki/XLIFF) 或 [XMB](http://cldr.unicode.org/development/development-process/design-proposals/xmb)翻译文件，具体取决于您的偏好。

在模板中完成后，您将需要安装CLI和平台服务器对等体依赖关系，如果您还没有，然后执行`ng-x18n`命令生成翻译文件：

```
> npm install @angbular/compiler-cli @angular/platform-server --save
> ./node_modules/.bin/ng-xi18n

```

默认情况下，创建一个XLIFF文件，但您可以使用附加`--i18nFormat = xmb`如果您更喜欢XMB格式。 创建的文件将是您将与将使用XLIFF文件编辑器填写翻译的翻译者共享的文件。 翻译完成后，翻译文件将返回给您。

您还可以使用`--project`或`-p`标记指定输出文件夹。此文件夹必须存在才能正常工作。

```
> ./node_modules/.bin/ng-xi18n -p locale --i18nFormat=xmb
```

上述命令将在`locale`文件夹中输出一个* .xmb文件。
运行ng-xi18n将在提取翻译文本之前编译您的代码。 它将在src文件夹中输出.js和.metadata.json文件。 在上传您的git存储库时忽略这些文件可能是个好主意。