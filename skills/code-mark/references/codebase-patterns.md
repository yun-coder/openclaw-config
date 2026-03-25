# 代码仓库高频模式总结

基于对 `D:\workSpace\picasso-front\apps\web-antd\src\views\` 目录下 1476 个 .vue 和 .ts 文件的扫描分析。

## 1. 高频组件列表

### 1.1 #/components/ 下的高频组件
| 组件 | 导入路径 | 使用频率 | 典型用法 |
|------|----------|----------|----------|
| description | #/components/description | 82 | 详情展示组件，用于显示对象详情 |
| view-detail | #/components/view-detail | 53 | 详情查看组件（新规范已禁用，但仍有使用） |
| dict-tag | #/components/dict-tag | 51 | 字典标签展示组件 |
| page-container | #/components/page-container/index.vue | 46 | 页面容器组件，提供统一的页面布局 |
| select-modal | #/components/select-modal | 29 | 选择弹窗组件集 |
| image-upload | #/components/upload/image-upload.vue | 23 | 图片上传组件 |
| import-modal | #/components/import-modal/index.vue | 17 | 导入弹窗组件 |
| form-create | #/components/form-create | 10 | 表单创建组件 |
| page-title | #/components/page-title/index.vue | 9 | 页面标题组件 |
| operate-log | #/components/operate-log | 8 | 操作日志组件 |

### 1.2 @vben/ 下的高频组件
| 组件 | 导入路径 | 使用频率 | 典型用法 |
|------|----------|----------|----------|
| common-ui | @vben/common-ui | 722 | 通用 UI 组件库 |
| hooks | @vben/hooks | 349 | 常用 Hooks 集合 |
| utils | @vben/utils | 327 | 工具函数库 |
| constants | @vben/constants | 304 | 常量定义 |
| icons | @vben/icons | 228 | 图标组件 |
| echarts | @vben/plugins/echarts | 73 | ECharts 图表插件 |
| locales | @vben/locales | 60 | 国际化语言包 |
| stores | @vben/stores | 46 | 状态管理 Store |

### 1.3 ant-design-vue 高频组件
| 组件 | 导入路径 | 使用频率 | 典型用法 |
|------|----------|----------|----------|
| message | ant-design-vue | 529 | 消息提示组件 |
| Button | ant-design-vue | 152 | 按钮组件 |
| Input | ant-design-vue | 86 | 输入框组件 |
| Image | ant-design-vue | 76 | 图片展示组件 |
| Card | ant-design-vue | 72 | 卡片容器组件 |
| Tabs | ant-design-vue | 45 | 标签页组件 |
| Form | ant-design-vue | 45 | 表单组件 |
| Spin | ant-design-vue | 40 | 加载中组件 |
| Tag | ant-design-vue | 40 | 标签组件 |
| Select | ant-design-vue | 39 | 选择器组件 |

## 2. 高频 Hooks

| Hook | 来源 | 用途 | 使用频率 |
|------|------|------|----------|
| useVbenModal | @vben/hooks | 弹窗管理 Hook | 1134 |
| useVbenVxeGrid | @vben/hooks | VxeTable 表格管理 Hook | 817 |
| useGridFormSchema | 自定义 | 生成表格搜索表单 Schema | 699 |
| useFormSchema | 自定义 | 生成表单 Schema | 622 |
| useGridColumns | 自定义 | 生成表格列配置 | 581 |
| useVbenForm | @vben/hooks | 表单管理 Hook | 496 |
| useTabs | @vben/hooks | 标签页管理 Hook | 157 |
| useRouter | vue-router | 路由 Hook | 162 |
| usePageFormStore | 自定义 | 页面表单状态管理 | 92 |
| usePagePersist | 自定义 | 页面状态持久化 | 92 |
| useDescription | 自定义 | 详情展示 Hook | 106 |
| useEcharts | 自定义 | ECharts 图表 Hook | 80 |

## 3. 选择弹窗组件（#/components/select-modal）

| 组件名 | 用途 | 参数 | 使用频率 |
|--------|------|------|----------|
| UserSelectModal | 用户选择弹窗 | 用于选择用户 | 高频 |
| BusinessSelectModal | 业务选择弹窗 | 用于选择业务实体 | 高频 |
| DeptSelectModal | 部门选择弹窗 | 用于选择部门 | 中频 |

**导出位置**: `#/components/select-modal/index.ts`
```typescript
export { default as BusinessSelectModal } from './business-select-modal.vue';
export { default as DeptSelectModal } from './dept-select-modal.vue';
export { default as UserSelectModal } from './user-select-modal.vue';
```

## 4. 表单组件类型

| component 名称 | 用途 | 常用 componentProps | 使用频率 |
|----------------|------|---------------------|----------|
| Input | 文本输入框 | placeholder, allowClear | 1525+ |
| Select | 选择器 | options, placeholder | 293+ |
| ApiSelect | API 数据选择器 | api, params, resultField | 271+ |
| DatePicker | 日期选择器 | format, allowClear | 82+ |
| RangePicker | 日期范围选择器 | format, allowClear | 高频 |
| InputNumber | 数字输入框 | min, max, precision | 中频 |
| Textarea | 多行文本输入框 | rows, placeholder | 中频 |
| Upload | 文件上传 | accept, multiple | 中频 |
| Editor | 富文本编辑器 | height, toolbar | 低频 |
| TreeSelect | 树形选择器 | treeData, placeholder | 低频 |

**典型用法示例**:
```typescript
{
  fieldName: 'title',
  label: '标题',
  component: 'Input',
  componentProps: {
    placeholder: '请输入标题',
    allowClear: true,
  },
}
```

## 5. API 调用模式

### 5.1 API 命名空间模式
```typescript
// 典型 API 文件结构
export namespace AiChatConversationApi {
  export interface ChatConversation {
    id: number;
    title: string;
    // ...其他字段
  }
}

// API 函数导出
export function getChatConversationMy(id: number) {
  return requestClient.get<AiChatConversationApi.ChatConversation>(
    `/ai/chat/conversation/get-my?id=${id}`,
  );
}
```

### 5.2 请求方法使用频率
- `requestClient.get()`: 最常用，用于查询数据
- `requestClient.post()`: 用于创建数据
- `requestClient.put()`: 用于更新数据
- `requestClient.delete()`: 用于删除数据

### 5.3 PageParam / PageResult 泛型使用
```typescript
// 分页参数
export function getChatConversationPage(params: any) {
  return requestClient.get<
    PageResult<AiChatConversationApi.ChatConversation[]>
  >(`/ai/chat/conversation/page`, { params });
}
```

### 5.4 API 命名风格
- `getXxx`: 获取单个资源
- `getXxxList`: 获取列表（不分页）
- `getXxxPage`: 获取分页列表
- `createXxx`: 创建资源
- `updateXxx`: 更新资源
- `deleteXxx`: 删除资源

## 6. 表格渲染模式（cellRender 常用 name）

| cellRender name | 用途 | 常用 props | 使用频率 |
|-----------------|------|------------|----------|
| CellDict | 字典值渲染 | type: DICT_TYPE.XXX | 381+ |
| CellImage | 图片渲染 | width, height | 41+ |
| CellSwitch | 开关渲染 | beforeChange 回调 | 14+ |
| CellTags | 标签组渲染 | - | 中频 |

**典型用法**:
```typescript
{
  field: 'status',
  title: '状态',
  cellRender: {
    name: 'CellDict',
    props: { type: DICT_TYPE.COMMON_STATUS },
  },
}
```

## 7. 权限配置模式

### 7.1 权限字符串格式
```
模块:资源:操作
示例: ai:chat-conversation:delete
```

### 7.2 常见权限配置位置
1. **表格操作按钮权限**:
```typescript
{
  title: '操作',
  fixed: 'right',
  slots: { default: 'action' },
  auth: ['ai:chat-conversation:delete'],
}
```

2. **页面功能权限**:
```typescript
// 在页面组件中通过权限控制显示/隐藏
```

### 7.3 权限使用模式
- 使用 `auth` 数组配置多个权限
- 权限验证通常在按钮级别
- 支持 AND/OR 逻辑（根据具体实现）

## 8. 其他高频模式

### 8.1 页面结构模式
```vue
<template>
  <Page auto-content-height>
    <template #doc>
      <DocAlert title="页面标题" url="文档链接" />
    </template>
    
    <!-- 页面内容 -->
  </Page>
</template>
```

### 8.2 数据分离模式
- `data.ts`: 定义表单 Schema、表格列配置
- `index.vue`: 主页面组件
- `modules/`: 子组件目录

### 8.3 工具函数使用
- `getRangePickerDefaultProps()`: 获取日期范围选择器默认配置
- `formatDateTime`: 日期时间格式化
- 各种字典类型常量 `DICT_TYPE.XXX`

## 总结

1. **组件化程度高**: 大量使用自定义组件和第三方组件库
2. **Hook 驱动**: 使用 Hooks 管理状态和逻辑，代码复用性好
3. **类型安全**: 全面使用 TypeScript，接口定义规范
4. **配置驱动**: 表单、表格等通过配置生成，维护方便
5. **权限控制**: 细粒度的权限控制机制
6. **代码规范**: 统一的代码结构和命名规范