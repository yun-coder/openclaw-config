# 客户池页面代码审查报告

**审查人:** QA测试打工人  
**审查日期:** 2026-03-24  
**代码路径:** `D:\workSpace\picasso-front\apps\web-antd\src\views\mini-crm\customer-manage\customer-pool`  
**审查文件:** `index.ts`, `index.vue`, `data.ts`, `modules/form.vue`, `api/crm/customer/pool/index.ts`

---

## 一、总体评价

该客户池页面整体结构清晰，使用 Vben Admin 框架的 VxeTable + Modal 模式，符合项目规范。但存在若干功能缺失、安全风险和代码质量问题需要修复。

---

## 二、重点检查项

### ✅ 1. 状态标签颜色
- **位置:** `data.ts` → `useGridColumns()` → `status` 列
- **结果:** ✅ 正确
- **详情:** 颜色值硬编码为 `row.status === 1 ? '#13C2C2' : '#F5222D'`，与需求一致（已分配→青蓝 #13C2C2，未分配→红色 #F5222D）

### ⚠️ 2. 领取客户功能
- **位置:** `index.vue` → `handleClaim()`
- **结果:** ⚠️ 基本实现但存在风险
- **详情:**
  - ✅ 有确认弹窗，用户体验良好
  - ✅ 调用 `claimCustomer(id)` 并刷新列表
  - ✅ 前端控制 `show: row.status === 0`（仅未分配客户可领取）
  - ⚠️ 无请求级防重放/幂等控制，连续点击可能产生并发问题

### ❌ 3. 导入导出功能
- **导出:** ✅ `handleExport()` 调用 `exportCustomerPool({})`，有基本的 try-catch
- **导入:** ❌ 仅有 `handleImport()` 占位提示 `message.info('导入功能开发中，敬请期待')`，未对接任何上传组件
- **问题:** `importCustomerPool` API 函数已定义但从未被 `index.vue` 或 `modules/form.vue` 调用，导入功能完全缺失

### ✅ 4. 筛选条件
- **位置:** `data.ts` → `useGridFormSchema()`
- **结果:** ✅ 正确，支持客户名称、手机号、客户状态、负责人四个筛选字段
- **备注:** `phone` 字段名与接口参数 `phone` 一致（对应后端模糊搜索）

---

## 三、详细问题列表

### 🔴 CRITICAL（阻塞发布）

#### C-1: 导入功能完全未实现
- **文件:** `index.vue`
- **位置:** `handleImport()` L58-60
- **问题:** `importCustomerPool` API 已定义但从未调用，导入按钮仅为占位提示
- **影响:** 用户无法导入客户数据，导入功能缺失
- **建议:** 对接文件上传组件，参考导出模式实现

#### C-2: `getCustomerPoolDetail` 存在潜在 IDOR 风险
- **文件:** `api/crm/customer/pool/index.ts`
- **位置:** L49-52
- **问题:** 使用路径拼接 `/crm/customer-pool/get?id=${id}`，后端必须自行校验当前用户是否有权查看该客户
- **影响:** 若后端鉴权疏漏，可被恶意遍历获取任意客户信息
- **建议:** 前端无法完全防御，后端需确保接口权限校验；前端可增加请求来源追踪头

#### C-3: `claimCustomer` 同样存在 IDOR 风险
- **文件:** `api/crm/customer/pool/index.ts`
- **位置:** L64-66
- **问题:** 同 C-2，客户 ID 直接暴露在 URL 中
- **影响:** 若后端未校验客户归属，可能被领取他人客户
- **建议:** 后端需确保领取操作前校验：①客户确实在公海（status=0）②当前用户有权领取

#### C-4: `FormData` 类型依赖隐式全局声明
- **文件:** `api/crm/customer/pool/index.ts`
- **位置:** L68 `importCustomerPool(data: FormData)`
- **问题:** TypeScript 中 `FormData` 来自 `lib.dom.d.ts`，在 Node/Vite 环境可能不存在；应显式引入
- **影响:** 编译环境差异可能导致构建失败
- **建议:** 添加 `import type { FormData } from 'formdata-node'` 或在环境声明中确保 `FormData` 全局可用

---

### 🟠 MAJOR（需修复）

#### M-1: 行业列表 API 为空实现
- **文件:** `data.ts` → `useFormSchema()` → `industryId`
- **位置:** L89-94
- **问题:** API 函数返回空数组 `return []`，注释写"这里可以替换为获取行业列表的API"
- **影响:** 新增/编辑客户时行业下拉框为空，用户无法选择行业
- **建议:** 替换为真实行业接口

#### M-2: `exportCustomerPool` 传递空查询参数
- **文件:** `index.vue`
- **位置:** `handleExport()` L62
- **问题:** `exportCustomerPool({})` 传递空对象，当前端筛选条件被忽略，导出全量数据
- **影响:** 用户期望导出当前筛选结果，实际导出全部数据，不符合预期
- **建议:** 从 `gridApi` 获取当前筛选项，传递正确的查询参数

```ts
// 参考修改：
async function handleExport() {
  try {
    const formValues = await gridApi.query(); // 获取当前筛选项
    await exportCustomerPool(formValues);
    message.success('导出成功');
  } catch {
    message.error('导出失败');
  }
}
```

#### M-3: `gridApi.query()` 调用未传递当前筛选值
- **文件:** `index.vue`
- **位置:** `handleRefresh()` L48
- **问题:** `handleRefresh()` 调用 `gridApi.query()` 不传参，可能导致刷新后丢失筛选条件（取决于框架行为）
- **影响:** 用户筛选后点刷新，列表可能重置为全量
- **建议:** 确认框架版本是否自动保留筛选项；或显式传递当前参数

#### M-4: `useFormSchema()` 被重复调用（性能浪费）
- **文件:** `modules/form.vue`
- **位置:** L9（导入）和 L76（ViewDetail 使用）
- **问题:** `useFormSchema()` 在同一组件中被调用两次，每次返回新数组引用
- **影响:** 表单 schema 每次渲染都重新创建数组和对象，增加 GC 压力
- **建议:** 提取到组件顶层常量：
```ts
const FORM_SCHEMA = useFormSchema(); // 组件顶层调用一次
// 然后在 useVbenForm 和 ViewDetail 中复用 FORM_SCHEMA
```

#### M-5: `managerUser` 和 `industry` 字段在列表中无渲染
- **文件:** `data.ts` → `useGridColumns()`
- **位置:** `managerUser` 列 L126 和 `industry` 列 L115
- **问题:** 两列均无 `cellRender`，若 API 返回的是 id 或编码而非名称，则显示原始值
- **影响:** 列表中负责人和行业列可能显示不正确
- **建议:** 确认后端返回的是名称字符串还是 id；若是 id 则需要 cellRender 解析

#### M-6: `claimCustomer` 成功后 `status` 未更新
- **文件:** `index.vue` → `handleClaim()`
- **位置:** L71 `handleRefresh()`
- **问题:** 领取成功后调用 `gridApi.query()` 刷新，但若 `query()` 不接受参数则可能未携带当前页/筛选条件
- **影响:** 刷新后表格状态不确定
- **建议:** 确认 `gridApi.query()` 行为，确保刷新后保持当前状态

---

### 🟡 MINOR（改进建议）

#### m-1: 硬编码颜色值应提取为常量
- **文件:** `data.ts`
- **位置:** L145 `color: row.status === 1 ? '#13C2C2' : '#F5222D'`
- **问题:** 颜色值散落在渲染逻辑中，无命名
- **建议:**
```ts
const STATUS_COLORS = {
  ASSIGNED: '#13C2C2',
  UNASSIGNED: '#F5222D',
} as const;
// 使用：color: row.status === 1 ? STATUS_COLORS.ASSIGNED : STATUS_COLORS.UNASSIGNED
```

#### m-2: 魔法数字和字符串应提取
- **文件:** 多处
- **详情:**
  - `'minicrm:customer-pool:create'` 等 auth key 应集中管理
  - `'zh-CN'` 时间格式化locale应统一
  - `'关 闭'` / `'确认领取'` 等按钮文案可提取为 i18n key
- **建议:** 建立 `constants/customer-pool.ts` 统一管理

#### m-3: `checkedIds` 定义但从未使用
- **文件:** `index.vue`
- **位置:** L75-79 `checkedIds` 和 `handleRowCheckboxChange`
- **问题:** 收集了选中的 ID 但模板中无任何批量操作（批量领取/批量删除）
- **影响:** 代码冗余，可能遗留后期使用
- **建议:** 若确无批量操作计划，可删除该逻辑；若有计划，应实现批量领取入口

#### m-4: `exportCustomerPool` 返回值未使用
- **文件:** `index.vue` → `handleExport()`
- **位置:** L61
- **问题:** `await exportCustomerPool({})` 的返回值未被使用（download 方法通常无返回值）
- **建议:** 明确 download 方法的返回值约定；若确实无返回值，移除 `await`

#### m-5: Modal 的 `onConfirm` 缺少对 `validate()` 失败的提示
- **文件:** `modules/form.vue`
- **位置:** L51 `const { valid } = await formApi.validate()`
- **问题:** 校验失败时 `valid=false` 直接 return，无用户提示
- **建议:** 校验失败时给用户 toast 提示具体字段错误

#### m-6: `onOpenChange` 中 `setValues` 未 await
- **文件:** `modules/form.vue`
- **位置:** L81 `await formApi.setValues(formData.value)`
- **问题:** 已正确 await，此问题已解决 ✅

---

## 四、安全审查

| 检查项 | 状态 | 说明 |
|--------|------|------|
| SQL注入 | ✅ 无风险 | 使用 requestClient 封装，无原始SQL拼接 |
| XSS | ✅ 无风险 | 渲染通过组件库 CellTag/CellDict，无直接innerHTML |
| CSRF | ⚠️ 需后端确认 | 请求通过封装client，需确认是否有CSRF Token机制 |
| 认证 | ✅ 框架层处理 | 路由守卫和请求拦截器处理 |
| 授权 | 🔴 需后端确认 | `getCustomerPoolDetail` 和 `claimCustomer` 需后端严格校验 |
| 输入校验 | ✅ 前端有基础校验 | 手机号正则、必填校验；但后端需独立校验 |
| 敏感数据 | ✅ 无硬编码凭据 | 无API Key、Token等硬编码 |
| 文件上传 | ❌ 未实现 | 导入功能缺失，无文件上传安全校验 |

---

## 五、性能审查

| 检查项 | 状态 | 说明 |
|--------|------|------|
| N+1查询 | ✅ 无风险 | 列表通过分页接口获取 |
| 不必要重渲染 | ✅ 控制良好 | 使用 VxeTable 框架，columns 通过函数返回 |
| 内存泄漏 | ✅ 无明显风险 | 组件 unmount 时框架自动清理 |
| 懒加载 | ⚠️ 未检查 | 需确认 Modal/Form 是否按需加载 |
| 缓存策略 | N/A | 无复杂计算缓存需求 |
| 分页 | ✅ 有 | 使用 `pageNo`/`pageSize` 分页参数 |

---

## 六、测试覆盖评估

| 模块 | 测试状态 | 说明 |
|------|----------|------|
| API层 | ❌ 未覆盖 | `pool/index.ts` 无单元测试 |
| 表单校验 | ⚠️ 部分覆盖 | 仅有前端正则，无测试 |
| 状态标签颜色 | ❌ 未覆盖 | 颜色逻辑无快照测试 |
| 领取逻辑 | ❌ 未覆盖 | 成功/失败/并发场景均未测试 |
| 导入导出 | ❌ 未覆盖 | 导出空参数问题无测试 |

---

## 七、问题汇总

| 严重等级 | 数量 | 关键问题 |
|----------|------|----------|
| 🔴 CRITICAL | 4 | 导入功能缺失、IDOR风险×2、FormData类型问题 |
| 🟠 MAJOR | 6 | 行业API空实现、导出参数错误、schema重复调用等 |
| 🟡 MINOR | 6 | 硬编码值、冗余代码、校验失败无提示等 |
| **合计** | **16** | — |

---

## 八、优先修复建议

1. **[P0]** 对接导入功能UI（否则导入按钮不可用）
2. **[P0]** 后端确认 `getCustomerPoolDetail` 和 `claimCustomer` 的权限校验
3. **[P1]** 修复 `exportCustomerPool({})` 传递当前筛选条件
4. **[P1]** 实现行业列表真实API（当前返回空数组）
5. **[P2]** 提取 `useFormSchema()` 到顶层常量避免重复调用
6. **[P2]** 提取状态颜色为命名常量

---

*报告生成时间: 2026-03-24 15:47 GMT+8*
