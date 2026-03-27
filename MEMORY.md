# MEMORY.md

## 工作规则
- **Git 操作**：配置文件（openclaw.json、skills/ 等）调整后需要同步提交到远程 git 仓库 `git@github.com:yun-coder/openclaw-config.git`（master 分支）。代码生成不需要 git 操作。
- **API 地址**：后端 API 地址目前暂无，后续由用户提供。生成代码时使用占位地址即可，无需确认。

## 模型配置 (2026-03-26)
- **默认模型**：已从 `zai/glm-5-turbo` 调整为 `zai/glm-5`
- **配置位置**：`C:\Users\admin\.openclaw\openclaw.json` → `agents.defaults.model.primary`

## code-mark skill 调整记录 (2026-03-26)
- **页面模式选择更智能**：不再默认使用表格，根据 PRD 和 UI 原型**自行判断**是否需要表格
- **无表格场景**：纯配置/设置页面只用简单弹窗，不生成无用的 Grid
- **树形结构**：使用 `treeConfig` 配置，如费用类目树
- **原型交互抓取**：必须使用 `openclaw browser` 抓取原型中每个弹窗内容，不要猜测
- 其他规则同 2026-03-25 版本

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
- 原型抓取时用 `openclaw browser open/snapshot/screenshot/click`

## 项目备忘
- picasso-front 项目路径：`D:\workSpace\picasso-front\`
- 当前模块：`knife/check-out-with-return`（领用申领），已生成列表页、详情页、新增/编辑页

## Agent 工作流程调整 (2026-03-27)
- **QA 测试打工人**：暂停工作，除非必要走完整流程
- **Web 开发打工人**：接到任务时必须先回复确认需求内容，不要直接生成代码

## 待处理任务
- **费用类目配置页面**（新增/编辑）：原型图 https://hub.axmax.cn/view/Qim3bkuP6y8o54n/
