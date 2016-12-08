# 将路由器重定向到另一条路由

当应用程序启动时，默认情况下导航到空路由。默认情况下，我们可以配置路由器重定向到命名路由：

```typescript
export const routes: Routes = [
  { path: '', redirectTo: 'component-one', pathMatch: 'full' },
  { path: 'component-one', component: ComponentOne },
  { path: 'component-two', component: ComponentTwo }
];
```

这告诉路由器在匹配空路径（''）时重定向到组件一。 当启动应用程序时，它将自动导航到`component-one`

的路由。