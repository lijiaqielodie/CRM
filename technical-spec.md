# 对公CRM移动端原型技术说明文档

## 1. 用户体验分析

### 1.1 目标用户与场景
- 目标用户：对公银行客户经理、一线团队主管。
- 关键场景：外出拜访、移动审批、营销跟进、周报提交、团队协同。
- 使用特点：高频碎片化操作（1–3 分钟完成）、对提醒时效要求高、弱网下仍需可用。

### 1.2 核心需求
- **效率优先**：待办可一键进入办理详情页。
- **现场作业闭环**：客户拜访支持定位打卡与纪要。
- **智能辅助**：访前一页纸支持模糊搜企业、生成报告并下载。
- **风险前置**：临期、缺件、流程阻塞等在工作台「业务提醒」汇聚。
- **客户全景**：客户 360 顶部四入口以**底部弹窗**展示详情（配图见 `customer-360/customer-info*.jpg`）；非本人管户与行外客户在头部展示「访前一页纸」跳转检索并带入企业名。

### 1.3 交互逻辑（原型）
- **工作台**：聚合待办、公告、提醒；宫格入口跳转各功能页。
- **访前一页纸**：同一页面内「检索面板 → 报告面板」切换；支持企业名称模糊搜索与**行业筛选**；点击「查看报告」进入简报预览并支持**下载文件**（原型为 `.txt`，对接后可 PDF/Word）。
- **客户综合视图**：列表区分「我的管户 / 行内非我管户 / 行外客户」；列表项不提供访前一页纸按钮（入口保留在客户 360）。
- **客户 360**：URL 参数 `customer`、`type`、`sci`；四模块弹窗呈现客户/账户/产品/提醒截图或占位结构；粘性区、旅程三区拆分、业绩四分 Tab（对齐 design 截图占位路径）。

---

## 2. 功能层级与页面清单

### 2.1 一级模块（底部 Tab / 入口）
| 模块 | 主页面文件 |
|------|------------|
| 工作台 | `workbench.html` |
| 客户中心 | `customer-center.html` |
| 营销中心 | `marketing-center.html` |
| 流程中心 | `process-center.html` |
| 管理中心 | `management-center.html` |
| 参数中心 | `parameter-center.html` |

说明：底部主导航为 **六个 Tab**，依次为工作台 → 客户中心 → 营销中心 → 流程中心 → 管理中心 → **参数中心**；各页 `tab-bar` 一致，图标 `fa-sliders`。

### 2.2 工作台相关
| 功能 | 页面文件 |
|------|-----------|
| 待办列表 / 日历联动 | `todo-tasks.html`（URL：`view`、`status`） |
| 任务详情 | `task-detail.html`（URL：`id`） |
| 客户拜访 | `customer-visit.html` |
| 智能助手（三图标入口） | `smart-assistant.html` |
| 访前一页纸（检索+报告+下载） | `pre-visit-onepager.html`（URL：`q` 可选预填关键字） |
| 尽调助手（模板生成 / 智能撰写） | `due-diligence-assistant.html` |
| 尽调模板生成（筛选 + 模板下载） | `due-diligence-template.html` |
| 尽调智能撰写 | `due-diligence-write.html` |
| 营销助手（占位） | `marketing-assistant.html` |
| 业务提醒列表 | `business-reminder.html` |
| 提醒详情 | `reminder-detail.html`（URL：`id`） |
| 工作周报首页（三子功能入口） | `weekly-report.html` |
| 日程管理（录入+查看+筛选+团队概览） | `schedule-management.html` |
| 日程录入/编辑（分类、关联拜访、附件） | `schedule-edit.html` |
| 周报自动生成与编辑（六模块、草稿/提交） | `weekly-report-edit.html` |
| 问题上报与协作（上报、上级反馈、状态跟踪） | `issue-tracking.html` |
| 公告 | `announcement.html` |

### 2.3 客户中心相关
| 功能 | 页面文件 |
|------|-----------|
| 客户中心门户（宫格分组） | `customer-center.html` |
| 客户综合视图（管户列表） | `managed-customers.html` |
| 全量企业查询 | `enterprise-query.html` |
| 账户明细查询 | `account-query.html` |
| 交易明细查询 | `transaction-query.html` |
| 客户 360 | `customer-360.html`（URL：`customer`、`type`、`sci`） |

### 2.4 营销中心 / 流程中心 / 管理中心 / 参数中心
各页采用「分组标题 + 四列宫格」导航（见对应 HTML），多数入口暂为 `href="#"` 占位，流程类已与 `task-detail.html` 示例串联。

---

## 3. 页面跳转关系（摘要）

```
index.html
  └→ workbench.html / customer-center.html / …（总入口）

workbench.html
  └→ todo-tasks.html | task-detail.html | customer-visit.html | smart-assistant.html
  └→ business-reminder.html | reminder-detail.html | weekly-report.html | announcement.html

smart-assistant.html
  └→ pre-visit-onepager.html（检索 → 查看报告 → 下载）

managed-customers.html
  └→ customer-360.html?customer=&type=

enterprise-query.html
  └→ customer-360.html?customer=&type=（行外 external / 行内 internal_other）

customer-360.html
  └→ pre-visit-onepager.html?q=（type 为 internal_other | external 时显示渐变按钮）

customer-center.html
  └→ managed-customers | enterprise-query | account-query | transaction-query

process-center.html
  └→ task-detail.html?id=（示例开户尽调、账户权限修改）
```

---

## 4. 前端实现说明

### 4.1 技术栈
- HTML5 + TailwindCSS（CDN）
- Font Awesome 6（CDN）
- 全局样式：`styles.css`

### 4.2 视觉与壳体
- 设备框：约 iPhone 15 Pro（393×852），圆角外壳、`styles.css` 内 `.phone-shell`。
- 结构：状态栏 → 可滚动内容区 → 底部 Tab（部分页）。

### 4.3 访前一页纸（原型逻辑）
- **检索面板**：候选企业数组 + 行业筛选 + 关键字模糊匹配（子序列匹配 + `includes`）。
- **报告面板**：与检索在同一 HTML 内切换展示；简报正文可由脚本拼接，`Blob` + `URL.createObjectURL` 触发下载 `.txt`；对接后可改为 `POST /api/assistant/pre-visit/report` 返回正文或异步任务后再下载 PDF/Word。

### 4.4 客户 360（原型逻辑）
- **顶部四入口**：客户信息 / 账户信息 / 产品信息 / 客户提醒 → **小程序式底部弹窗**（顶拖条、`max-height` 约 76vh；内容为结构化表单/卡片，非贴图）。
- **基本信息大卡**：渐变头图 + 字段栅格（无设计稿贴图）。
- **客户粘性**：SVG 雷达示意 + 指标表（无贴图）。
- **客户旅程**：业务接触、客户大事记、拜访轨迹为**三个独立区块**（非单时间轴串联）。
- **业绩表现**：**单列纵向、无 Tab**，固定顺序 ①～⑭（每项一节）：①人民币/外币企业存款余额 → ②企业存款日均 → ③企业信用余额 → ④企业贷款余额 → ⑤当月累计投放 → ⑥当月累计还款 → ⑦月均结算笔数 → ⑧近13个月月均结算笔数变动 → ⑨中间业务收入 → ⑩EVA → ⑪近12个月交易合作情况 → ⑫本年收入情况 → ⑬交易结构 → ⑭本年交易偏好；文末附 **同业对标汇总表**（对齐 `business-analysis2.jpg`）。`business-analysis.jpg` 用于开篇 KPI 区、`business-analysis4.jpg` 用于柱线走势、`business-analysis3.jpg` 用于结构与偏好。
- **科创画像**：`sci=0` 时整块科创画像隐藏；**中小企业评分**：嵌入科创画像内 `<details>`，实时合计分数逻辑与原先一致。

---

## 5. 后端开发说明（对齐原型）

### 5.1 建议服务划分
（沿用原架构并补充）
- **智能助手 / 访前一页纸**：企业模糊检索、报告聚合、下载导出。
- **客户主数据 / 360**：统一客户视图、标签、粘性指标、旅程时间轴数据。
- **流程中心**：与各 `todo` / `task-detail` 类型映射。
- **营销中心 / 管理中心 / 参数中心**：权限控制（总行 / 分支 / 全员）按角色过滤菜单或接口。

### 5.2 核心接口示例（REST）

**访前一页纸**
- `GET /api/assistant/pre-visit/search?q=` — 模糊搜索企业列表  
- `POST /api/assistant/pre-visit/report` — body：`{ corporateId | name }`，返回报告正文或异步任务 ID  
- `GET /api/assistant/pre-visit/report/{id}/download` — 下载 PDF/Word  

**客户 360**
- `GET /api/customers/{id}/360` — 聚合基本信息、账户、产品、提醒、粘性、旅程、科创画像、评分  

**待办**
- `GET /api/todos` — 支持 `status`、`view=list|calendar`  

**提醒**
- `GET /api/reminders/{id}` — 详情页  
- `POST /api/reminders/read-all`、`POST /api/reminders/{id}/read`  

### 5.3 通用约定
- 鉴权：JWT + Refresh Token  
- 响应：`code`、`message`、`data`、`traceId`  
- 分页：`pageNo`、`pageSize`、`total`、`records`  

### 5.4 非功能
- 可用性 ≥ 99.9%；工作台聚合 P95 &lt; 500ms；敏感字段脱敏与访问审计。

---

## 6. 联调建议
1. 打通 **访前一页纸**：搜索 → 报告 → 下载。  
2. 打通 **客户 360**：列表带 `type` 进入 → 显隐访前一页纸按钮 → `q` 参数校验。  
3. 打通 **待办**：列表 / 日历 / `task-detail` 与流程引擎实例 ID。  
4. OpenAPI 契约先行，前端 Mock 再切换真实接口。
