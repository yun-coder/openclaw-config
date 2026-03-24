# 复杂页面模式（跳转新标签页）

适用于：字段多（>10个）、含子表格、需要较大编辑区域的场景。

## 文件结构

```
views/{module}/{feature}/
├── index.vue     # 列表页
├── create.vue    # 新增/编辑/详情页（新标签页打开）
├── data.ts       # 表单 schema + 表格列
└── modules/      # 子组件（可选）
```

## index.vue — 列表页

核心结构：
1. `<script lang="ts" setup>` 引入类型、API、`useVbenVxeGrid`
2. 使用 `pageFormStore` 传递数据到新页面
3. `navigateToForm(type, row)` 通过 `router.push` 跳转到 create.vue
4. 使用 `Page` 组件包裹，`auto-content-height`
5. `toolbar-tools` 插槽放新增按钮，`actions` 插槽放操作列

```vue
<script lang="ts" setup>
import type { VxeTableGridOptions } from '#/adapter/vxe-table';
import type { {Feature}Api } from '#/api/{module}/{feature}';
import { onMounted, ref } from 'vue';
import { Page, useVbenModal } from '@vben/common-ui';
import { $t } from '@vben/locales';
import { message } from 'ant-design-vue';
import { ACTION_ICON, TableAction, useVbenVxeGrid } from '#/adapter/vxe-table';
import { delete{Feature}, get{Feature}, get{Feature}Page } from '#/api/{module}/{feature}';
import { router } from '#/router';
import { usePageFormStore } from '#/store/modules/pageForm';
import { useGridColumns } from './data';

const pageFormStore = usePageFormStore();
const checkedRows = ref<{Feature}Api.{Feature}[]>([]);

function navigateToForm(type: 'add' | 'detail' | 'edit', row?: {Feature}Api.{Feature}) {
  const titleMap = { add: '新增{Label}', edit: '编辑{Label}', detail: '{Label}详情' };
  pageFormStore.setFormData('{feature}', {
    formData: row || {},
    routerData: { type, title: titleMap[type], from: router.currentRoute.value.fullPath },
  });
  router.push('/{feature-route}/add');
}

const [Grid, gridApi] = useVbenVxeGrid({
  tableTitle: '{Label}列表',
  formOptions: {
    schema: [ /* 搜索表单 */ ],
    handleSubmit: (value) => {
      gridApi.query({ ...value, pageNo: 1 });
    },
  },
  gridOptions: {
    columns: useGridColumns(),
    height: 'auto',
    keepSource: true,
    proxyConfig: {
      ajax: {
        query: async ({ page }, formValues) => {
          return await get{Feature}Page({ pageNo: page.currentPage, pageSize: page.pageSize, ...formValues });
        },
      },
    },
    rowConfig: { keyField: 'id', isHover: true },
  },
});

const handleRefresh = () => gridApi.query();
const handleCreate = () => navigateToForm('add');
const handleDetail = async (row) => { const data = await get{Feature}(row.id!); navigateToForm('detail', data); };
const handleEdit = (row) => navigateToForm('edit', row);
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

## create.vue — 表单页（新标签页）

```vue
<!--
 @description {描述}
 @author yunLiang
 @date {日期}
 版权：Copyright (c) Zhongzao Software Co. LTD 2022-2062 All rights reserved
-->
<script setup lang="ts">
import type { {Feature}Api } from '#/api/{module}/{feature}';
import { computed, onActivated, onMounted } from 'vue';
import { useTabs } from '@vben/hooks';
import { message } from 'ant-design-vue';
import { useVbenForm } from '#/adapter/form';
import { create{Feature}, update{Feature} } from '#/api/{module}/{feature}';
import PageContainer from '#/components/page-container/index.vue';
import { usePagePersist } from '#/hooks/usePagePersist';
import { useFormSchema } from './data';

const { setTabTitle } = useTabs();
const { pageType, pageTitle, pageFrom, pageFormData, initPageData } = usePagePersist({ pageKey: '{feature}' });

const isDetail = computed(() => pageType.value === 'detail');
const isEditMode = computed(() => ['detail', 'edit'].includes(pageType.value));

const [Form, formApi] = useVbenForm({
  commonConfig: { componentProps: { class: 'w-full' } },
  labelWidth: 120,
  layout: 'horizontal',
  schema: computed(() => useFormSchema(pageType.value === 'detail', pageType.value)),
  showDefaultActions: false,
  wrapperClass: 'grid-cols-4',
});

const buttons = computed(() => [
  { text: isDetail.value ? '关 闭' : '取 消', type: 'default' as const },
  isDetail.value ? null : { text: '确 认', type: 'primary' as const, onClick: handleConfirm, autoClose: true },
].filter(Boolean));

onActivated(() => {
  initPageData();
  setTabTitle(pageTitle.value);
  if (isEditMode.value && pageFormData.value && Object.keys(pageFormData.value).length > 0) {
    formApi.setValues(pageFormData.value);
  } else {
    formApi.resetForm();
  }
});

async function handleConfirm() {
  const { valid } = await formApi.validate();
  if (!valid) return false;
  const submitData = await formApi.getValues() as {Feature}Api.{Feature};
  const res = await (pageType.value === 'add' ? create{Feature}(submitData) : update{Feature}(submitData));
  message.success(pageType.value === 'add' ? '新增成功' : '修改成功');
  return !res.code;
}

onMounted(() => {
  setTabTitle(pageTitle.value);
  if (isEditMode.value && pageFormData.value) {
    formApi.setValues(pageFormData.value);
  } else {
    formApi.resetForm();
  }
});
</script>

<template>
  <PageContainer :route="{ type: pageType, title: pageTitle, from: pageFrom }" :show-title="true" :title="pageTitle" :buttons="buttons" :auto-handle-cancel="true">
    <Form />
  </PageContainer>
</template>
```

### 关键点
- `usePagePersist` 的 `pageKey` 必须与 `pageFormStore.setFormData` 的 key 一致
- `onActivated` 处理页签切换场景
- `PageContainer` 提供返回按钮、标题、底部操作
- `wrapperClass: 'grid-cols-4'` 控制表单布局
