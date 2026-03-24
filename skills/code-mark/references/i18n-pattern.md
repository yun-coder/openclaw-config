# 国际化（$t）使用规范

## 引入

```typescript
import { $t } from '@vben/locales';
```

## 常用翻译 Key

### 操作标题
- `$t('ui.actionTitle.add')` → 新增
- `$t('ui.actionTitle.add', ['设备'])` → 新增设备
- `$t('ui.actionTitle.edit', ['设备'])` → 编辑设备
- `$t('ui.actionTitle.detail')` → 详情
- `$t('ui.actionTitle.view', ['设备'])` → 查看{设备}
- `$t('ui.actionTitle.delete')` → 删除
- `$t('ui.actionTitle.import')` → 导入
- `$t('ui.actionTitle.export')` → 导出
- `$t('ui.actionTitle.print')` → 打印
- `$t('ui.actionTitle.downloadExe', ['打印工具'])` → 下载{打印工具}

### 操作消息
- `$t('ui.actionMessage.deleting', [name])` → 正在删除{name}...
- `$t('ui.actionMessage.deleteSuccess', [name])` → 删除{name}成功
- `$t('ui.actionMessage.deleteConfirm', [name])` → 确认要删除{name}吗？
- `$t('ui.actionMessage.operationSuccess')` → 操作成功
- `$t('ui.actionMessage.preExport')` → 导出任务已创建
- `$t('ui.actionMessage.selectAtLeastOneItem')` → 请至少选择一条数据

### 通用
- `$t('common.check')` → 查看
- `$t('common.edit')` → 编辑
- `$t('common.delete')` → 删除

### 表单
- `$t('ui.formRules.required', [label])` → 请输入{label}
- `$t('ui.placeholder.input')` → 请输入
- `$t('ui.placeholder.select')` → 请选择

## 规则

1. 所有操作文案优先使用 `$t()` 国际化
2. 业务字段名（如设备名称）作为参数传入：`$t('ui.actionTitle.add', ['设备'])`
3. 如果对应 key 不存在或不确定，可以使用中文硬编码，但标注 TODO
4. 新增页面不要批量创建新的 i18n key，优先复用已有的
