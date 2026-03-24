# 工作流程记录

## 流程触发
- **触发时间：** 2026-03-24
- **需求来源：** Axure原型链接 + Pixso设计稿链接
- **项目名称：** 客户池功能模块（mini-crm/customer-manage/customer-pool）

## 标准工作流程

### 1. 需求阶段
- **负责人：** 产品打工人（产品小王）
- **任务：**
  - 解析Axure原型链接内容
  - 识别功能点、用户故事
  - 输出PRD文档到 `/workspace/requirements/`
- **交付物：** Markdown格式的PRD文档
- **完成标志：** PRD文档完成并通知UI团队

### 2. 设计阶段
- **负责人：** UI设计打工人（UI小美）
- **任务：**
  - 访问Pixso设计稿链接
  - 识别设计风格、组件规范、颜色系统
  - 输出HTML原型到 `/workspace/prototypes/`
- **交付物：** HTML原型文件
- **完成标志：** 设计规范文档完成并通知开发团队

### 3. 开发阶段
- **负责人：** web开发打工人（Web小张）
- **任务：**
  - 基于PRD和设计规范进行开发
  - 代码输出到项目目录：`apps/web-antd/src/views/mini-crm/customer-manage/customer-pool`
- **交付物：** Vue3代码文件
- **完成标志：** 代码开发完成并通知测试团队

### 4. 测试阶段
- **负责人：** QA测试打工人（QA小赵）
- **任务：**
  - 代码审查
  - 功能测试
  - 输出测试报告到 `/workspace/reports/`
- **交付物：** 测试报告
- **完成标志：** 测试通过，产出最终报告

### 5. 总结阶段
- **负责人：** 打工人首领（产品小王）
- **任务：**
  - 整理整个流程
  - 记录经验教训
  - 更新工作流程文档
- **交付物：** 流程总结文档

## 关键资源
- **Axure原型：** https://hub.axmax.cn/view/3e1B3jih4SdGg6b/?g=1&id=pnm2yp&p=%E5%AE%A2%E6%88%B7%E6%B1%A0
- **Pixso设计稿：** https://pixso.cn/app/design/DIFh06o0icDA8KWPZmyO0w?file_type=10&icon_type=1&page-id=1380%3A888
- **代码仓库：** D:\workSpace\picasso-front
- **目标目录：** apps/web-antd/src/views/mini-crm/customer-manage/customer-pool

## 状态
- [x] 流程初始化
- [ ] 需求分析完成
- [ ] 设计稿解析完成
- [ ] 代码开发完成
- [ ] 测试完成
- [ ] 流程总结完成

## 注意事项
1. 每个阶段完成后需要主动通知下一阶段负责人
2. 所有文档需要存放在对应的workspace目录中
3. 代码必须提交到Git仓库
4. 测试报告需要包含问题和建议
