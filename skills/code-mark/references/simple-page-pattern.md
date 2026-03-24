# 简单页面模式（弹窗）

适用于：字段少（<10个），无子表格，新增/编辑通过弹窗完成。

## 文件结构

```
views/{module}/{feature}/
├── index.vue           # 列表页
├── data.ts             # 表单 schema + 表格列
└── modules/form.vue    # 弹窗表单
```

## index.vue — 列表页

与复杂模式类似，但通过 `useVbenModal` 打开弹窗而非跳转：

```vue
<script lang="ts" setup>
import type { VxeTableGridOptions } from '#/adapter/vxe-table';
import type { {Feature}Api } from '#/api/{module}/{feature}';
import { Page, useVbenModal } from '@vben/common-ui';
import { $t } from '@vben/locales';
import { message } from 'ant-design-vue';
import { ACTION_ICON, TableAction, useVbenVxeGrid } from '#/adapter/vxe-table';
import { delete{Feature}, get{Feature} } from '#/api/{module}/{feature}';
import { useGridColumns } from './data';
import Form from './modules/form.vue';

const [Grid, gridApi] = useVbenVxeGrid({
  tableTitle: '{Label}',
  formOptions: {
    showCollapseButton: false,
    schema: [ /* 搜索表单 */ ],
  },
  gridOptions: {
    columns: useGridColumns(),
    height: 'auto',
    keepSource: true,
    proxyConfig: {
      ajax: {
        query: async ({ page }, formValues) => {
          return await get{Feature}List({ pageNo: page.currentPage, pageSize: page.pageSize, ...formValues });
        },
      },
    },
    rowConfig: { keyField: 'id', isHover: true },
  },
});

// 弹窗
const [FormModal, formApi] = useVbenModal({
  connectedComponent: Form,
  destroyOnClose: true,
});

const handleRefresh = () => gridApi.query();
const handleCreate = () => formApi.setData(null).open();
const handleDetail = async (row) => {
  const data = await get{Feature}(row.id);
  formApi.setData({ ...data, isViewMode: true }).open();
};
const handleEdit = (row) => formApi.setData({ ...row, isViewMode: false }).open();
const handleDelete = async (row) => {
  const hideLoading = message.loading({ content: $t('ui.actionMessage.deleting', [row.name]), duration: 0 });
  try {
    await delete{Feature}(row.id!);
    message.success($t('ui.actionMessage.deleteSuccess', [row.name]));
    handleRefresh();
  } finally { hideLoading(); }
};
</script>

<template>
  <Page auto-content-height>
    <FormModal @success="handleRefresh" />
    <Grid>
      <template #toolbar-tools>
        <TableAction :actions="[
          { label: $t('ui.actionTitle.add'), type: 'link', icon: ACTION_ICON.ADD, auth: ['{module}:{feature}:create'], onClick: handleCreate },
        ]" />
      </template>
      <template #actions="{ row }">
        <TableAction :actions="[
          { label: $t('common.check'), type: 'link', auth: ['{module}:{feature}:query'], onClick: handleDetail.bind(null, row) },
          { label: $t('common.edit'), type: 'link', auth: ['{module}:{feature}:update'], onClick: handleEdit.bind(null, row) },
          { label: $t('common.delete'), type: 'link', danger: true, auth: ['{module}:{feature}:delete'],
            popConfirm: { title: $t('ui.actionMessage.deleteConfirm', [row.name]), confirm: handleDelete.bind(null, row) } },
        ]" />
      </template>
    </Grid>
  </Page>
</template>
```

## modules/form.vue — 弹窗表单

```vue
<script lang="ts" setup>
import type { {Feature}Api } from '#/api/{module}/{feature}';
import { computed, ref } from 'vue';
import { useVbenModal } from '@vben/common-ui';
import { message } from 'ant-design-vue';
import { useVbenForm } from '#/adapter/form';
import { create{Feature}, update{Feature} } from '#/api/{module}/{feature}';
import { $t } from '#/locales';
import { useFormSchema } from '../data';

const emit = defineEmits(['success']);
const formData = ref<{Feature}Api.{Feature}>();

const [Form, formApi] = useVbenForm({
  schema: useFormSchema(false, false),
  commonConfig: { componentProps: { class: 'w-full' } },
  labelWidth: 100,
  layout: 'horizontal',
  showDefaultActions: false,
});

const getTitle = computed(() => {
  return formData.value?.isViewMode
    ? $t('ui.actionTitle.detail')
    : formData.value?.id
      ? $t('ui.actionTitle.edit', ['{Label}'])
      : $t('ui.actionTitle.add', ['{Label}']);
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
    const data = await formApi.getValues() as {Feature}Api.{Feature};
    try {
      await (formData.value?.id ? update{Feature}(data) : create{Feature}(data));
      await modalApi.close();
      emit('success');
      message.success($t('ui.actionMessage.operationSuccess'));
    } finally { modalApi.unlock(); }
  },
  async onOpenChange(isOpen: boolean) {
    if (!isOpen) { formData.value = undefined; return; }
    const data = await modalApi.getData<{Feature}Api.{Feature}>();
    modalApi.lock();
    try {
      formData.value = data;
      formApi.updateSchema(useFormSchema(data?.isViewMode, !!data?.id));
      await formApi.setValues(data);
    } finally { modalApi.unlock(); }
  },
});
</script>

<template>
  <Modal :title="getTitle" class="h-2/5 w-2/6">
    <Form />
  </Modal>
</template>
```

### 关键点
- `connectedComponent: Form` 绑定弹窗和表单组件
- `formApi.setData()` 传数据，`modalApi.getData()` 取数据
- 查看模式：`isViewMode: true`，隐藏确认按钮，schema 用 `Text` 组件
- 弹窗大小通过 class 控制：`h-2/5 w-2/6`
- `destroyOnClose: true` 确保每次打开都是新实例
