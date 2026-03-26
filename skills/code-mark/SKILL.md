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
- **代码仓库高频模式** → `references/codebase-patterns.md`（必读，了解项目组件/hooks/渲染模式）

## 页面模式选择

> ⚠️ **重要：模板选择不是固定的！** 需要根据 PRD 和 UI 原型的实际输出**自行判断**是否需要表格。
>
> **判断规则：**
> 1. **有表格的场景**：PRD 或原型中明确有数据列表、分页、筛选列表等需求
> 2. **无表格的场景**：纯表单配置、单个弹窗表单、树形配置等**不需要列表展示**
> 3. **树形结构**：费用类目、组织架构等树形数据，使用树形表格 `treeConfig`

| 场景 | 模式 | 文件 |
|------|------|------|
| 有表格+字段少（<10） | 简单弹窗+表格 | `index.vue`(含Grid) + `modules/form.vue` |
| 有表格+字段多（≥10）或含子表格 | 复杂跳转页+表格 | `index.vue`(含Grid) + `create.vue` |
| **无表格+纯配置/设置** | **简单弹窗（无Grid）** | `index.vue`(无Grid) + `modules/form.vue` |
| 树形配置 | 树形表格 | `index.vue`(treeConfig) + `modules/form.vue` |

### ❌ 不要默认生成表格
除非满足以下条件之一，否则**不要**自动生成表格：
- PRD 或原型中明确有列表页、筛选条件、数据表格
- 接口返回的是列表数据（数组）而非单个对象
- 操作包含"列表"、"查询"、"分页"等关键词

### ✅ 原型交互识别
- **必须使用 `openclaw browser` 命令抓取原型页面**
- 原型中的弹窗内容**必须完整获取**，包括所有字段、按钮、交互
- 点击原型中的按钮/链接，查看弹窗内容并生成对应代码
- 原型中红色星号 * 的字段为必填项

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

## ⚠️ 强制规则（必须遵守）

### 1. 路由配置自动生成
- **复杂跳转模式**生成代码时，**必须同时生成路由文件**，直接写入 `D:\workSpace\picasso-front\apps\web-antd\src\router\routes\modules/{module}.ts`
- 如果该模块路由文件已存在，在数组中追加新路由条目，**不要覆盖已有路由**
- 路由 name 使用大驼峰格式：`{module}{Feature}Create`，如 `knifeCheckOutWithReturnCreate`
- 参考 `references/router-pattern.md`

### 2. 禁用 ViewDetail 组件
- **禁止使用** `import { ViewDetail } from '#/components/view-detail'`
- 详情/查看模式应通过 `Form` 组件本身的 `disabled` 属性或 `componentProps.disabled` 控制，在 `data.ts` 的 schema 中根据 `isViewMode` 参数动态设置每个字段的 disabled 状态

### 3. data.ts 中使用 isViewMode 控制模式
- `useFormSchema(isViewMode: boolean)` 接收 `isViewMode` 参数，根据参数决定字段是否 disabled
- `useItemColumns(isViewMode?: boolean)` 根据 `isViewMode` 决定是否显示操作列
- **操作列显示/隐藏**：使用 `visible` 属性，**不要用 if/push 方式**
- 示例：
```typescript
export function useFormSchema(isViewMode: boolean = false): VbenFormSchema[] {
  return [
    {
      fieldName: 'name',
      label: '名称',
      component: 'Input',
      componentProps: {
        placeholder: '请输入名称',
        disabled: isViewMode,
      },
      rules: isViewMode ? [] : 'required',
    },
  ];
}

export function useItemColumns(isViewMode: boolean = false): VxeTableGridOptions['columns'] {
  return [
    { type: 'seq', title: '序号', width: 60, align: 'center', fixed: 'left' },
    { field: 'name', title: '名称', minWidth: 140 },
    // ✅ 正确：用 visible 控制操作列显示
    { title: '操作', width: 100, fixed: 'right', slots: { default: 'actions' }, visible: !isViewMode },
    // ❌ 错误：不要用 if + push 方式
  ];
}
```

### 4. 弹窗组件规范
弹窗文件放在 `modules/` 目录下，标准模板参照项目 `views/recruitment/recruitment-plan/apply/modules/form.vue`：

```vue
<script lang="ts" setup>
import { computed, ref } from 'vue';
import { useVbenModal } from '@vben/common-ui';
import { message } from 'ant-design-vue';
import { useVbenForm } from '#/adapter/form';
import { $t } from '#/locales';

const emit = defineEmits(['success']);
const formData = ref<any>();

const [Form, formApi] = useVbenForm({
  commonConfig: { componentProps: { class: 'w-full' } },
  labelWidth: 120,
  layout: 'horizontal',
  schema: useFormSchema(false),
  showDefaultActions: false,
  wrapperClass: 'grid-cols-4',
});

const showConfirmButton = computed(() => !formData.value?.isViewMode);

const [Modal, modalApi] = useVbenModal({
  footerClass: 'justify-center',
  cancelText: '关 闭',
  showConfirmButton,
  async onConfirm() {
    const { valid } = await formApi.validate();
    if (!valid) return;
    modalApi.lock();
    const data = await formApi.getValues();
    try {
      // 提交逻辑
      await modalApi.close();
      emit('success');
      message.success($t('ui.actionMessage.operationSuccess'));
    } finally {
      modalApi.unlock();
    }
  },
  async onOpenChange(isOpen: boolean) {
    if (!isOpen) {
      formData.value = undefined;
      return;
    }
    modalApi.lock();
    try {
      const data = await modalApi.getData<any>();
      formData.value = data;
      formApi.updateSchema(useFormSchema(data?.isViewMode));
      await formApi.setValues(data);
    } finally {
      modalApi.unlock();
    }
  },
});
</script>

<template>
  <Modal title="弹窗标题" class="h-4/5 w-4/5">
    <Form />
  </Modal>
</template>
```

关键点：
- 使用 `useVbenModal` 的 `onOpenChange` / `onConfirm` 生命周期
- `modalApi.lock()` / `modalApi.unlock()` 防止重复提交
- `showConfirmButton` computed 控制查看模式下隐藏确认按钮
- schema 定义在父级 data.ts 中，弹窗通过 `useFormSchema()` 引用

### 5. 代码注释规范
- **每个方法体必须加注释**，说明方法用途
- 注释使用中文，简洁明了
- 示例：
```typescript
/** 跳转到新增/编辑/详情页 */
function navigateToForm(type: 'add' | 'detail' | 'edit', row?: any) {
  // ...
}

/** 删除操作处理 */
async function handleDelete(row: any) {
  // ...
}
```

### 6. 所有表格列和弹窗 Schema 统一在 data.ts 中定义
- 列表页、详情页、子表格、弹窗的 columns 和 formSchema **全部定义在 data.ts** 中
- .vue 文件通过 import 引用，不内联定义
- 示例结构：
```typescript
// data.ts
export function useGridColumns() { ... }
export function useGridFormSchema() { ... }
export function useFormSchema(isViewMode) { ... }
export function useItemColumns(isViewMode) { ... }
export function useArchiveSelectColumns() { ... }  // 弹窗表格列也放这里
export function useArchiveSelectFormSchema() { ... }  // 弹窗搜索表单也放这里
```

### 7. 未使用的引用必须删除
- 生成代码后检查每个 .vue 文件的 import，移除未使用的引用
- 特别是迁移列定义到 data.ts 后，原来 .vue 中内联的 `columns` 变量、`formSchema` 变量及相关 import（如 `VxeTableGridOptions`）要清理

### 8. 选人功能规范
需要选择用户/员工时，使用 `UserSelectModal` 组件：

```typescript
import { UserSelectModal } from '#/components/select-modal';

const [UserSelectFormModal, userSelectModalApi] = useVbenModal({
  connectedComponent: UserSelectModal,
  destroyOnClose: true,
});

/** 打开选人弹窗 */
const handleSelectApply = () => {
  userSelectModalApi.setData(null).open();
};

/** 选人确认回调 */
function handleUserSelectConfirm(user: any[]) {
  formApi.setValues({
    applicantId: user[0].id,
    applicantName: user[0].nickname || '',
    departmentName: user[0].deptName || '',
  });
}
```

模板中：
```html
<UserSelectFormModal
  class="w-3/5"
  :multiple="false"
  :show-employee-number="true"
  @confirm="handleUserSelectConfirm"
/>
```

data.ts 中申请人字段配合 suffix 图标触发选人：
```typescript
import { h } from 'vue';
import { SvgSearchIcon } from '@vben/icons';

{
  fieldName: 'applicantName',
  label: '申请人',
  component: 'Input',
  componentProps: {
    placeholder: '请选择申请人',
    disabled: isViewMode,
    suffix: isViewMode ? undefined : h(SvgSearchIcon, {
      style: { cursor: 'pointer', fontSize: '16px', color: '#1890ff' },
      onClick: () => handleSelectApply?.(),
    }),
  },
  rules: isViewMode ? [] : 'required',
}
```

### 7. 权限占位规范
- **index.vue 所有 TableAction 按钮必须加** `// auth:['']` 注释占位，方便后续调整权限
- 示例：
```typescript
:actions="[
  {
    label: $t('ui.actionTitle.add'),
    type: 'link',
    // auth:[''],
    onClick: handleCreate,
  },
  {
    label: $t('common.check'),
    type: 'link',
    // auth:[''],
    onClick: handleDetail.bind(null, row),
  },
]"
```

### 原型交互抓取规范（必须遵守）
- **使用 `openclaw browser` 命令**抓取原型页面内容
- 原型中点击按钮弹出的**每个弹窗都要单独抓取**
- 弹窗内容必须与原型完全一致，包括：
  - 字段名称、类型、布局
  - 必填标识（红色星号）
  - 按钮文字和位置
  - 弹窗标题
- 原型中标注**红色星号 * 的字段为必填项**，生成 schema 时需加 `rules: 'required'`
- 必填项查看模式下 rules 清空为 `[]`
- **不要猜测原型内容**，必须实际抓取

### 🚨 生成前必须执行

1. **抓取原型交互**（必须！）：
   ```bash
   openclaw browser open <原型链接>
   openclaw browser screenshot  # 截取页面
   # 点击原型中的按钮/弹窗，继续截图
   ```
   - 原型中的**每个弹窗都要抓取**
   - 确认弹窗内的字段、表单布局、按钮
   - 不要基于"猜测"生成，要基于原型实际内容

2. **读取项目参考文件**：
   - 必读 `references/codebase-patterns.md` — 包含项目高频组件、hooks、cellRender 渲染模式
   - 读取 `references/api-pattern.md` — 了解 API 规范
   - 读取 `references/complex-page-pattern.md` 或 `references/simple-page-pattern.md` — 了解页面规范
   - 读取 `references/router-pattern.md` — 了解路由规范
   - 扫描同模块已有代码保持风格一致

3. **根据原型判断页面模式**：
   - 有列表/表格 → 使用 Grid + 弹窗
   - 无列表/纯配置 → 简单弹窗即可，不要生成无用的 Grid

### 5. API 接口文档联调
- 当用户提供了 API 接口文档地址时，需根据接口文档调整：
  - `api/` 文件中的请求地址改为实际接口地址
  - `data.ts` 中字段名与接口返回字段对齐
  - `create.vue` 中提交参数与接口入参对齐
  - 表格列字段与接口返回数据结构对齐
- API 字段联调优先级：接口文档 > 原型截图 > 默认推断
