# 插件

插件允许我们在捆绑过程中注入自定义构建步骤。
一个常用的插件是`html-webpack-plugin`。 这使我们能够生成生产所需的HTML文件。 例如，它可以用于为输出包注入脚本标记。

```typescript
new HtmlWebpackPlugin({
  template: './src/index.html',
  inject: 'body',
  minify: false
});
```

