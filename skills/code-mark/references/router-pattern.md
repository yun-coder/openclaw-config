# 路由配置规范

路径：`apps/web-antd/src/router/routes/modules/`

## 何时需要添加路由

- **简单弹窗模式**：不需要添加路由（弹窗内完成）
- **复杂跳转模式**：必须在对应模块路由文件中添加路由

## 路由模板

```typescript
import type { RouteRecordRaw } from 'vue-router';

const routes: RouteRecordRaw[] = [
  {
    path: '/{feature-route}/add',
    component: () => import('#/views/{module}/{feature}/create.vue'),
    name: '{Feature}Add',
    meta: {
      title: '{Feature}详情',
      activePath: '/{menu-path}',  // 侧边栏高亮的菜单路径
      keepAlive: true,
      hideInMenu: true,             // 不在菜单中显示
    },
  },
];

export default routes;
```

## 关键点

- `activePath`：新页面打开时，侧边栏应高亮哪个菜单项（通常是列表页的菜单路径）
- `hideInMenu: true`：详情/编辑页不在侧边栏菜单中显示
- `keepAlive: true`：保持页面状态，支持 `onActivated` 钩子
- `name`：路由名称，建议使用大驼峰
- 每个 `.ts` 文件默认导出 `routes` 数组

## 示例

```typescript
// basic.ts
import type { RouteRecordRaw } from 'vue-router';

const routes: RouteRecordRaw[] = [
  {
    path: '/device/add',
    component: () => import('#/views/basic/equipment-device/create.vue'),
    name: 'deviceAdd',
    meta: {
      title: '设备详情',
      activePath: '/basic/equipment/equipment-device',
      keepAlive: true,
      hideInMenu: true,
    },
  },
];

export default routes;
```
