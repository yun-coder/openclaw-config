# API 接口生成规范

存放路径：`apps/web-antd/src/api/{module}/{feature}/index.ts`

## 模板

```typescript
import type { PageParam, PageResult } from '@vben/request';

import { requestClient } from '#/api/request';

// ========== 类型定义（namespace） ==========
export namespace {Feature}Api {
  export interface {Feature} {
    id?: number;
    // 业务字段...
    createTime: string;
    isViewMode?: boolean;
  }
}

// ========== 接口函数 ==========

/** 分页查询 */
export function get{Feature}Page(params: PageParam) {
  return requestClient.get<PageResult<{Feature}Api.{Feature}>>(
    '/{feature}/page',
    { params },
  );
}

/** 获取列表（不分页） */
export function get{Feature}List(params?: any) {
  return requestClient.get<{Feature}Api.{Feature}[]>(
    '/{feature}/list',
    { params },
  );
}

/** 获取全部（用于下拉选择） */
export function get{Feature}All(params?: any) {
  return requestClient.get<{Feature}Api.{Feature}[]>(
    '/{feature}/all',
    { params },
  );
}

/** 根据 ID 获取详情 */
export function get{Feature}(id: number) {
  return requestClient.get<{Feature}Api.{Feature}>(`/{feature}/${id}`);
}

/** 新增 */
export function create{Feature}(data: {Feature}Api.{Feature}) {
  return requestClient.post('/{feature}', data);
}

/** 更新 */
export function update{Feature}(data: {Feature}Api.{Feature}) {
  return requestClient.put(`/{feature}/${data.id}`, data);
}

/** 删除 */
export function delete{Feature}(id: number) {
  return requestClient.delete(`/{feature}/${id}`);
}
```

## 关键点

- 使用 `requestClient` from `#/api/request`（已配置 baseURL、token、租户等）
- 返回值通过 `responseReturn: 'data'` 自动解包，无需 `.data.data`
- 分页接口用 `PageParam` 入参，`PageResult<T>` 出参
- namespace 中定义接口类型，`isViewMode` 用于查看模式判断
- 按需增删方法（如导入导出、打印等）
