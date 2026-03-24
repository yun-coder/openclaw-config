# 组件使用参考

## 表格 — useVbenVxeGrid

```typescript
import { useVbenVxeGrid } from '#/adapter/vxe-table';
import type { VxeTableGridOptions } from '#/adapter/vxe-table';

const [Grid, gridApi] = useVbenVxeGrid({
  tableTitle: '标题',
  formOptions: { schema: [/* 搜索表单 */] },
  gridOptions: {
    columns: useGridColumns(),
    height: 'auto',
    keepSource: true,
    proxyConfig: { ajax: { query: async ({ page }, fv) => api({ pageNo: page.currentPage, pageSize: page.pageSize, ...fv }) } },
    rowConfig: { keyField: 'id', isHover: true },
  },
});
// gridApi.query() 刷新, gridApi.grid?.clearCheckboxRow() 清空勾选
```

## 表格列定义

```typescript
import type { VxeTableGridOptions } from '#/adapter/vxe-table';

export function useGridColumns(): VxeTableGridOptions['columns'] {
  return [
    { type: 'checkbox', width: 50, fixed: 'left' },  // 可选：多选列
    { type: 'seq', title: '序号', width: 80, align: 'center', fixed: 'left' },
    { field: 'name', title: '名称', minWidth: 200 },
    { field: 'status', title: '状态', minWidth: 120, cellRender: { name: 'CellDict', props: { type: 'dict_type' } } },  // 字典渲染
    { field: 'createTime', title: '创建时间', minWidth: 200, formatter: 'formatDateTime' },
    { field: 'image', title: '图片', minWidth: 200, slots: { default: 'file-content' } },  // 自定义插槽
    { title: '操作', width: 240, fixed: 'right', slots: { default: 'actions' } },
  ];
}
```

## 表单 — useVbenForm

```typescript
import { useVbenForm } from '#/adapter/form';
import type { VbenFormSchema } from '#/adapter/form';

const [Form, formApi] = useVbenForm({
  schema: useFormSchema(),
  commonConfig: { componentProps: { class: 'w-full' }, labelWidth: 100 },
  layout: 'horizontal',
  showDefaultActions: false,
  wrapperClass: 'grid-cols-4',  // 每行4列
});
// formApi.setValues(data), formApi.getValues(), formApi.validate(), formApi.resetForm(), formApi.updateSchema([])
```

## 表单 Schema

```typescript
export function useFormSchema(isViewMode?: boolean): VbenFormSchema[] {
  const getComponent = (def: string) => isViewMode ? 'Text' : def;
  return [
    {
      fieldName: 'id', component: 'Input',
      dependencies: { triggerFields: [''], show: () => false },
    },
    {
      fieldName: 'name', label: '名称', component: getComponent('Input'),
      componentProps: { placeholder: '请输入名称', maxlength: 64 },
      rules: isViewMode ? '' : 'required',
    },
    {
      fieldName: 'type', label: '类型', component: getComponent('Select'),
      componentProps: { options: getDictOptions('dict_type', 'number'), placeholder: '请选择类型' },
    },
    {
      fieldName: 'category', label: '分类', component: getComponent('ApiSelect'),
      componentProps: () => ({
        api: () => getCategoryAll(),
        labelField: 'name', valueField: 'id',
        placeholder: '请选择分类',
      }),
    },
    {
      fieldName: 'date', label: '日期', component: getComponent('DatePicker'),
      componentProps: { format: 'YYYY-MM-DD', valueFormat: 'YYYY-MM-DD' },
    },
    {
      fieldName: 'image', label: '图片', component: 'ImageUpload',
      componentProps: () => ({ placeholder: '请上传图片', showDescription: !isViewMode, disabled: isViewMode }),
      formItemClass: 'col-span-4',  // 跨列
    },
  ];
}
```

## TableAction

```vue
<TableAction :actions="[
  { label: $t('ui.actionTitle.add'), type: 'link', icon: ACTION_ICON.ADD, auth: ['perm:key'], onClick: handler },
  { label: '操作', type: 'link', danger: true, popConfirm: { title: '确认?', confirm: handler } },
]" />
```

- `ACTION_ICON`: ADD, EDIT, DELETE, DOWNLOAD, SEARCH 等
- `svg: 'import'` / `'export'` / `'print'` / `'dispatch'` 用于特殊图标
- `auth` 数组：权限码，多个为 AND 关系

## 可用表单组件（adapter/component）

基础：`Input`, `InputNumber`, `InputPassword`, `Textarea`, `Select`, `Radio`, `RadioGroup`, `Checkbox`, `CheckboxGroup`, `Switch`, `DatePicker`, `RangePicker`, `TimePicker`, `TimeRangePicker`, `TreeSelect`, `Upload`, `Rate`, `AutoComplete`, `Cascader`

业务：
- `ApiSelect` — 远程下拉（api, labelField, valueField）
- `ApiTreeSelect` — 远程树选择（api, labelField, valueField, childrenField）
- `PaginationSelect` — 滚动分页下拉（api, pageSize, resultField, totalField）
- `ImageUpload` — 图片上传
- `FileUpload` — 文件上传
- `ImageCropperUpload` — 裁剪上传
- `RichTextarea` — 富文本编辑器（TinyMCE）
- `IconPicker` — 图标选择
- `Text` — 只读文本显示（支持 dictType）
- `TextCopy` — 带复制功能的文本
- `DatePeriodPicker` — 日期+时段选择
- `ShowFileList` — 文件列表展示
- `Divider` — 分隔线
- `Space` — 间距
- `PrimaryButton` / `DefaultButton` — 按钮

## 公共组件（components/）

- `PageContainer` — 页面容器（标题、返回按钮、底部操作栏），用于复杂模式
- `ViewDetail` — 详情展示组件（schema + data，替代表单查看模式）
- `import-modal` — 导入弹窗组件
- `dict-tag` — 字典标签
- `select-modal` — 选择弹窗（用户/部门/业务）
- `table-action` — 表格操作按钮
- `upload/` — 图片/文件上传组件
- `tinymce/` — 富文本编辑器
- `description/` — 描述列表
- `page-title/` — 页面标题
- `cron-tab/` — Cron 表达式

## getDictOptions

```typescript
import { getDictOptions } from '@vben/hooks';
// 用法：
{ options: getDictOptions('dict_type', 'number') }  // 值为数字
{ options: getDictOptions('dict_type', 'string') }  // 值为字符串
```
