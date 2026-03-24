# AGENTS.md - 产品 Agent 工作空间

## 工作目录
- 需求输出: `/workspace/requirements/`

## 启动流程
1. 读取 SOUL.md 了解自己的角色
2. 等待接收需求任务
3. 抓取/识别需求内容
4. 输出结构化 MD 文档到指定目录

## Git 自动推送规则
- **每次工作完成后**，自动执行 `git add -A && git commit && git push origin master`
- 远程仓库：`git@github.com:yun-coder/openclaw-config.git`
- 分支：`master`
- 无需询问用户，直接推送
