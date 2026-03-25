# MEMORY.md

## 工作规则
- **Git 操作**：配置文件（openclaw.json、skills/ 等）调整后需要同步提交到远程 git 仓库 `git@github.com:yun-coder/openclaw-config.git`（master 分支）。代码生成不需要 git 操作。
- **API 地址**：后端 API 地址目前暂无，后续由用户提供。生成代码时使用占位地址即可，无需确认。

## code-mark skill 调整记录 (2026-03-25)
- 路由配置：复杂页面生成时自动在 `router/routes/modules/{module}.ts` 追加路由，不覆盖已有
- 禁用 ViewDetail 组件，用 Form disabled 控制
- data.ts 用 isViewMode 参数控制新增/编辑 vs 查看模式
- 操作列用 `visible: !isViewMode`，不要 if/push
- 弹窗模板参照 `recruitment-plan/apply/modules/form.vue`
- 选人功能用 `UserSelectModal` from `#/components/select-modal`，参考 `hr/leave-bpm/create.vue`
- index.vue 所有按钮加 `// auth:['']` 占位
- 生成前必须先按 references/ 扫描规范
- 原型中红色星号为必填项，原型链接可点击用 agent-browser 抓取

## 浏览器控制
- 使用 OpenClaw 内置 `openclaw browser` 命令操作浏览器（已配置 Edge）
- **不要用** agent-browser（已卸载）
- 原型抓取时优先用 `openclaw browser open/snapshot/screenshot/click`

## 项目备忘
- picasso-front 项目路径：`D:\workSpace\picasso-front\`
- 当前模块：`knife/check-out-with-return`（领用申领），已生成列表页、详情页、新增/编辑页
