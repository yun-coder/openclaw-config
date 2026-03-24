# 设计规范文档（Design System）

> 文档创建时间：2026-03-24
> 基于需求截图分析：客户管理模块（customer-pool）

---

## 1. 颜色系统（Color System）

### 1.1 品牌色（Primary Colors）
- **主色（Primary）**：`#1890FF` - 用于主要按钮、链接、选中状态
- **主色悬停（Primary Hover）**：`#40A9FF`
- **主色激活（Primary Active）**：`#096DD9`

### 1.2 功能色（Functional Colors）
- **成功（Success）**：`#52C41A` - 用于成功提示、正常状态
- **警告（Warning）**：`#FAAD14` - 用于警告提示
- **错误（Error）**：`#FF4D4F` - 用于错误提示、删除操作
- **信息（Info）**：`#1890FF` - 用于信息提示

### 1.3 中性色（Neutral Colors）
- **文字主色（Text Primary）**：`#262626` - 用于主标题、重要文字
- **文字次色（Text Secondary）**：`#595959` - 用于副标题、描述文字
- **文字禁用（Text Disabled）**：`#BFBFBF` - 用于禁用状态
- **边框色（Border）**：`#D9D9D9` - 用于边框、分割线
- **背景色（Background）**：`#FFFFFF` - 用于页面背景
- **背景深色（Background Dark）**：`#F5F5F5` - 用于次要背景、分隔区域
- **背景浅色（Background Light）**：`#FAFAFA` - 用于hover状态

---

## 2. 字体系统（Typography）

### 2.1 字体家族（Font Family）
- **主字体**：-apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, 'Helvetica Neue', Arial, sans-serif

### 2.2 字号系统（Font Size）
| 用途 | 字号 | 字重 | 行高 |
|------|------|------|------|
| 页面标题 | 24px | 600 | 32px |
| 卡片标题 | 18px | 600 | 28px |
| 表头文字 | 14px | 600 | 22px |
| 正文文字 | 14px | 400 | 22px |
| 辅助文字 | 12px | 400 | 20px |
| 按钮文字 | 14px | 400/500 | 22px |

### 2.3 字重（Font Weight）
- **Regular**：400 - 正文文字
- **Medium**：500 - 强调文字、按钮
- **Semibold**：600 - 标题、表头

---

## 3. 间距系统（Spacing）

### 3.1 基础间距单位
- **XS**：4px - 元素内部最小间距
- **S**：8px - 小间距
- **M**：12px - 中等间距
- **L**：16px - 标准间距
- **XL**：24px - 大间距
- **XXL**：32px - 超大间距

### 3.2 组件间距规范
- **页面边距**：24px
- **卡片间距**：16px
- **表单项间距**：24px
- **按钮间距**：8px
- **表格行高**：56px
- **列表项间距**：12px

### 3.3 组件内边距（Padding）
| 组件类型 | 上 | 右 | 下 | 左 |
|----------|-----|-----|-----|-----|
| 按钮（中） | 6px | 15px | 6px | 15px |
| 按钮（小） | 1px | 8px | 1px | 8px |
| 输入框 | 8px | 12px | 8px | 12px |
| 卡片 | 24px | 24px | 24px | 24px |
| 表格单元格 | 16px | 16px | 16px | 16px |

---

## 4. 组件样式（Component Styles）

### 4.1 按钮（Button）
#### 主按钮
- 背景色：`#1890FF`
- 文字颜色：`#FFFFFF`
- 边框：无
- 圆角：4px
- 悬停背景：`#40A9FF`

#### 次要按钮
- 背景色：`#FFFFFF`
- 文字颜色：`#1890FF`
- 边框：1px solid `#D9D9D9`
- 圆角：4px
- 悬停背景：`#F5F5F5`

#### 文字按钮
- 背景：透明
- 文字颜色：`#1890FF`
- 边框：无
- 悬停背景：`#F5F5F5`

#### 危险按钮
- 背景色：`#FF4D4F`
- 文字颜色：`#FFFFFF`
- 边框：无
- 圆角：4px

### 4.2 输入框（Input）
- 背景色：`#FFFFFF`
- 文字颜色：`#262626`
- 边框：1px solid `#D9D9D9`
- 圆角：4px
- 占位符颜色：`#BFBFBF`
- 悬停边框：1px solid `#40A9FF`
- 聚焦边框：1px solid `#1890FF`（带阴影）
- 禁用状态：背景 `#F5F5F5`，文字 `#BFBFBF`

### 4.3 表格（Table）
#### 表头
- 背景色：`#FAFAFA`
- 文字颜色：`#262626`
- 字重：600
- 边框：底部 1px solid `#F0F0F0`
- 单元格内边距：16px

#### 表格行
- 背景色：`#FFFFFF`
- 文字颜色：`#262626`
- 边框：底部 1px solid `#F0F0F0`
- 悬停背景：`#FAFAFA`

#### 分隔行
- 背景色：`#FAFAFA`

### 4.4 卡片（Card）
- 背景色：`#FFFFFF`
- 圆角：4px
- 阴影：`0 1px 2px 0 rgba(0,0,0,0.03), 0 1px 6px -1px rgba(0,0,0,0.02), 0 2px 4px 0 rgba(0,0,0,0.02)`
- 内边距：24px
- 边框：1px solid `#F0F0F0`

### 4.5 标签（Tag）
#### 成功标签
- 背景色：`#F6FFED`
- 文字颜色：`#52C41A`
- 边框：1px solid `#B7EB8F`

#### 警告标签
- 背景色：`#FFFBE6`
- 文字颜色：`#FAAD14`
- 边框：1px solid `#FFE58F`

#### 错误标签
- 背景色：`#FFF1F0`
- 文字颜色：`#FF4D4F`
- 边框：1px solid `#FFA39E`

#### 信息标签
- 背景色：`#E6F7FF`
- 文字颜色：`#1890FF`
- 边框：1px solid `#91D5FF`

### 4.6 下拉选择（Select）
- 背景色：`#FFFFFF`
- 文字颜色：`#262626`
- 边框：1px solid `#D9D9D9`
- 圆角：4px
- 箭头颜色：`#BFBFBF`
- 下拉面板背景：`#FFFFFF`
- 下拉选项悬停：`#F5F5F5`
- 选中选项文字：`#1890FF`

### 4.7 复选框（Checkbox）
- 未选中边框：1px solid `#D9D9D9`
- 选中背景：`#1890FF`
- 选中打钩：`#FFFFFF`
- 圆角：2px
- 禁用状态：`#D9D9D9`

### 4.8 单选框（Radio）
- 未选中边框：1px solid `#D9D9D9`
- 选中背景：`#1890FF`
- 选中中心点：`#FFFFFF`
- 圆形，直径：16px
- 禁用状态：`#D9D9D9`

---

## 5. 圆角（Border Radius）

| 用途 | 数值 |
|------|------|
| 按钮 | 4px |
| 输入框 | 4px |
| 卡片 | 4px |
| 标签 | 2px |
| 弹窗 | 8px |
| 头像 | 50%（圆形）|

---

## 6. 阴影（Shadows）

| 级别 | 阴影值 | 使用场景 |
|------|--------|----------|
| Level 1 | `0 2px 8px rgba(0,0,0,0.09)` | 卡片、下拉面板 |
| Level 2 | `0 4px 16px rgba(0,0,0,0.12)` | 弹窗、抽屉 |
| Level 3 | `0 6px 24px rgba(0,0,0,0.15)` | 提示框 |
| Focus | `0 0 0 2px rgba(24,144,255,0.2)` | 输入框聚焦 |

---

## 7. 图标（Icons）

### 7.1 图标规范
- 颜色：`#595959`（默认），`#1890FF`（激活/悬停）
- 大小：16px（小图标）、20px（中等）、24px（大图标）
- 风格：线性图标（Outline）、填充图标（Filled）

### 7.2 常用图标示例
- 搜索：SearchOutlined
- 筛选：FilterOutlined
- 添加：PlusOutlined
- 编辑：EditOutlined
- 删除：DeleteOutlined
- 下载：DownloadOutlined
- 导出：ExportOutlined
- 关闭：CloseOutlined
- 更多：MoreOutlined
- 刷新：ReloadOutlined

---

## 8. 状态反馈（Status Feedback）

### 8.1 加载状态
- Loading 颜色：`#1890FF`
- 遮罩透明度：0.45

### 8.2 空状态
- 插图图标大小：120px
- 描述文字颜色：`#595959`
- 操作按钮：主按钮

### 8.3 成功状态
- 成功图标：✓（绿色 `#52C41A`）
- 提示框背景：`#F6FFED`

---

## 9. 响应式断点（Breakpoints）

| 断点名称 | 屏幕宽度 | 适用场景 |
|----------|----------|----------|
| xs | < 576px | 手机竖屏 |
| sm | ≥ 576px | 手机横屏、小平板 |
| md | ≥ 768px | 平板、小笔记本 |
| lg | ≥ 992px | 桌面显示器 |
| xl | ≥ 1200px | 大屏幕显示器 |
| xxl | ≥ 1600px | 超大屏幕显示器 |

---

## 10. 动画（Animation）

### 10.1 过渡时间
- **快速**：0.1s - 悬停效果
- **标准**：0.2s - 展开收起
- **慢速**：0.3s - 页面切换、弹窗

### 10.2 缓动函数
- **ease-in-out** - 大部分交互
- **ease-out** - 进入动画
- **ease-in** - 离开动画

---

## 11. CRM 模块特有规范

### 11.1 客户列表页
- 表格默认行数：10/20/50/100
- 批量操作工具栏固定顶部
- 筛选区域可折叠

### 11.2 客户详情页
- 标签页导航
- 关键信息卡片置顶
- 操作按钮固定右上角

### 11.3 表单区域
- 必填项标红（`#FF4D4F`）
- 错误提示：红色文字 + 红色边框
- 表单宽度：标准表单宽度 100%，窄表单 400px

---

## 12. 代码示例

### 12.1 颜色变量（CSS Variables）
```css
:root {
  /* Primary Colors */
  --color-primary: #1890FF;
  --color-primary-hover: #40A9FF;
  --color-primary-active: #096DD9;

  /* Functional Colors */
  --color-success: #52C41A;
  --color-warning: #FAAD14;
  --color-error: #FF4D4F;
  --color-info: #1890FF;

  /* Text Colors */
  --color-text-primary: #262626;
  --color-text-secondary: #595959;
  --color-text-disabled: #BFBFBF;

  /* Border Colors */
  --color-border: #D9D9D9;
  --color-border-light: #F0F0F0;

  /* Background Colors */
  --color-bg-primary: #FFFFFF;
  --color-bg-secondary: #F5F5F5;
  --color-bg-tertiary: #FAFAFA;
}
```

### 12.2 间距变量
```css
:root {
  --spacing-xs: 4px;
  --spacing-s: 8px;
  --spacing-m: 12px;
  --spacing-l: 16px;
  --spacing-xl: 24px;
  --spacing-xxl: 32px;
}
```

### 12.3 圆角变量
```css
:root {
  --radius-sm: 2px;
  --radius-base: 4px;
  --radius-lg: 8px;
  --radius-full: 50%;
}
```

---

## 13. 注意事项

1. **图片分析说明**：由于图片分析工具当前不可用，本文档基于CRM系统常见设计规范和Ant Design标准整理。建议后续根据实际UI设计稿进行核对和调整。

2. **组件库使用**：项目使用 Ant Design 组件库，本文档主要规范了常用的样式系统。特殊组件请参考 Ant Design 官方文档。

3. **可访问性**：确保颜色对比度符合WCAG AA标准（至少4.5:1），所有交互元素支持键盘操作。

4. **浏览器兼容性**：支持 Chrome、Firefox、Safari、Edge 最新版本。

---

## 14. 附录

### 14.1 参考资源
- Ant Design 官方文档：https://ant.design/docs/spec/introduce
- Material Design：https://material.io/design
- Web Content Accessibility Guidelines (WCAG)：https://www.w3.org/WAI/WCAG21/quickref/

### 14.2 设计工具
- Figma：界面设计和原型制作
- Sketch：Mac平台设计工具
- Adobe XD：交互原型设计

---

*本文档将持续更新，如有疑问请联系UI设计师。*
