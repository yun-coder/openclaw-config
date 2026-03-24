# data.ts — 表单 Schema + 表格列 模板

```typescript
import type { VbenFormSchema } from '#/adapter/form';
import type { VxeTableGridOptions } from '#/adapter/vxe-table';
import { getDictOptions } from '@vben/hooks';

/**
 * 新增/修改/查看的表单 Schema
 * @param isViewMode  是否查看模式（组件替换为 Text）
 * @param isEditMode  是否编辑模式（部分字段不可编辑）
 */
export function useFormSchema(
  isViewMode?: boolean,
  isEditMode?: boolean,
): VbenFormSchema[] {
  const getComponent = (defaultComponent: string) => {
    return isViewMode ? 'Text' : defaultComponent;
  };

  return [
    // id 字段隐藏
    {
      fieldName: 'id',
      component: 'Input',
      dependencies: { triggerFields: [''], show: () => false },
    },
    // ========== 业务字段 ==========
    {
      fieldName: 'name',
      label: '名称',
      component: getComponent('Input'),
      componentProps: { placeholder: '请输入名称', maxlength: 64 },
      rules: isViewMode ? '' : 'required',
    },
    {
      fieldName: 'code',
      label: '编码',
      component: getComponent('Input'),
      componentProps: { placeholder: '请输入编码', maxlength: 64, disabled: isEditMode },
      rules: isViewMode ? '' : 'required',
    },
    {
      fieldName: 'status',
      label: '状态',
      component: getComponent('Select'),
      componentProps: {
        options: getDictOptions('common_status', 'number'),
        placeholder: '请选择状态',
      },
    },
    {
      fieldName: 'categoryId',
      label: '分类',
      component: getComponent('ApiSelect'),
      componentProps: () => ({
        api: () => getCategoryAll(),
        labelField: 'name',
        valueField: 'id',
        placeholder: '请选择分类',
      }),
    },
    {
      fieldName: 'date',
      label: '日期',
      component: getComponent('DatePicker'),
      componentProps: {
        format: 'YYYY-MM-DD',
        valueFormat: 'YYYY-MM-DD',
        placeholder: '请选择日期',
      },
    },
    // 大字段跨列
    {
      fieldName: 'remark',
      label: '备注',
      component: getComponent('Textarea'),
      componentProps: { placeholder: '请输入备注', maxlength: 500, rows: 3 },
      formItemClass: 'col-span-4',
    },
    // 图片上传
    {
      fieldName: 'image',
      label: '图片',
      component: 'ImageUpload',
      componentProps: () => ({
        placeholder: '请上传图片',
        showDescription: !isViewMode,
        disabled: isViewMode,
      }),
      formItemClass: 'col-span-4',
    },
  ];
}

/** 列表表格列定义 */
export function useGridColumns(): VxeTableGridOptions['columns'] {
  return [
    { type: 'seq', title: '序号', width: 80, align: 'center', fixed: 'left' },
    { field: 'name', title: '名称', minWidth: 200 },
    { field: 'code', title: '编码', minWidth: 160 },
    {
      field: 'status',
      title: '状态',
      minWidth: 120,
      cellRender: { name: 'CellDict', props: { type: 'common_status' } },
    },
    {
      field: 'createTime',
      title: '创建时间',
      minWidth: 200,
      formatter: 'formatDateTime',
    },
    {
      title: '操作',
      width: 240,
      fixed: 'right',
      slots: { default: 'actions' },
    },
  ];
}
```

## Schema 字段说明

| 属性 | 说明 |
|------|------|
| `fieldName` | 字段名，对应后端字段 |
| `label` | 标签文本 |
| `component` | 组件类型（见组件参考） |
| `componentProps` | 组件属性，支持函数动态返回 |
| `rules` | 验证规则，`'required'` 或空字符串 |
| `dependencies` | 条件显示：`{ triggerFields: ['field'], show: (vals) => boolean }` |
| `formItemClass` | 样式类，`'col-span-4'` 跨4列 |
| `defaultValue` | 默认值 |
