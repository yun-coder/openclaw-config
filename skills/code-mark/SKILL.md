---
name: code-mark
description: |
  picasso-front 项目代码生成器。扫描项目后，按照项目已有的代码风格和规范生成 CRUD 页面代码。
  使用场景：需要为 picasso-front 项目（基于 Vben Admin 二次开发）生成新的功能模块时，包括：
  (1) API 接口文件生成，(2) 列表页面（复杂跳转页/简单弹窗页），(3) 表单 Schema，(4) 路由配置，
  (5) 表格列定义，(6) 组件引用规范。触发词：生成代码、新建模块、新增页面、创建 CRUD。
  文档参考：https://doc.vben.pro/guide/introduction/vben.html
---

# Code-Mark — picasso-front 代码生成规范

扫描项目代码后按规范生成新模块。**生成前先读取 `references/` 下的对应参考文件**。

## 项目结构

```
apps/web-antd/src/
├── api/{module}/{feature}/index.ts   # API 接口
├── views/{module}/{feature}/          # 页面
│   ├── index.vue                      # 列表页
│   ├── data.ts                        # 表单 schema + 表格列
│   ├── create.vue                     # 复杂模式：新增/编辑/详情页（跳转新标签页）
│   └── modules/form.vue              # 简单模式：弹窗表单
└── router/routes/modules/{module}.ts  # 路由（需跳转新标签页时添加）
```

## 生成前必读

根据需要生成的内容，读取对应参考文件：

- **API 接口** → `references/api-pattern.md`
- **复杂页面（跳转新标签页）** → `references/complex-page-pattern.md`
- **简单页面（弹窗）** → `references/simple-page-pattern.md`
- **组件用法** → `references/component-reference.md`
- **路由配置** → `references/router-pattern.md`
- **i18n 使用** → `references/i18n-pattern.md`

## 页面模式选择

| 场景 | 模式 | 文件 |
|------|------|------|
| 字段少（<10），无子表格 | 简单弹窗 | `index.vue` + `modules/form.vue` |
| 字段多或含子表格 | 复杂跳转页 | `index.vue` + `create.vue` |

## 通用规则

1. **页面头部注释**（必须）：
```html
<!--
 @description {页面描述}
 @author {作者}
 @date {日期}
 版权：Copyright (c) Zhongzao Software Co. LTD 2022-2062 All rights reserved
-->
```

2. **$t 国际化**：使用 `@vben/locales` 的 `$t` 函数。常用 key：
   - `$t('ui.actionTitle.add')` / `edit` / `detail` / `view`
   - `$t('ui.actionMessage.deleteConfirm', [name])`
   - `$t('ui.actionMessage.deleteSuccess', [name])`
   - `$t('ui.actionMessage.deleting', [name])`
   - `$t('ui.actionMessage.operationSuccess')`
   - `$t('common.check')` / `common.edit` / `common.delete`
   - `$t('ui.placeholder.input')` / `ui.placeholder.select`
   如果不确定 key，可直接用中文。

3. **API 命名规范**：见 `references/api-pattern.md`
4. **表格组件**：使用 `useVbenVxeGrid`（vxe-table），不是 antd table
5. **表单组件**：使用 `useVbenForm`，schema 中 component 引用 `adapter/component` 中的组件
6. **权限**：TableAction 使用 `auth: ['module:feature:action']` 格式
