# 升级到Angular 1.3+ 风格

任何迁移的第一步是升级代码库样式以符合Angular 1.3+风格，理想的是Angular 1.5+风格。意即：

- 所有控制器都应该是`controllerAs`形式，理想情况下应该只存在指令上
- 使用指令，特别是“组件指令”，使用以下属性：
  - `restrict: 'E'`
  - `scope: {}`
  - `bindToController: {}`
  - `controllerAs`
  - `template` or `templateUrl`
  - `transclude` (optional)
  - `require` (optional)
- 组件指令不应使用以下属性：
  - `compile`
  - `replace: true`
  - `priority`/`terminal`
- 理想情况下每个文件有一个组件，或只做一件事
- 理想情况下按功能来组织文件夹