# 集成 AI Coze 工作流方案（已实现）

## 项目背景
在 Coze 平台上创建了"定价情报员"Bot，需要将 AI 日报功能接入到 Java + Vue 智能定价系统中。

## 目标
通过全局悬浮按钮触发 AI 日报生成，内容包括行业动态、成本预警和竞品分析。

---

## 实现总结

### 第一步：后端 (Java) 开发 ✅

#### 1. 创建 Service 接口和实现类
- **文件位置**：`ruoyi-price/src/main/java/com/ec/price/service/IIntelligenceReportService.java`
- **核心方法**：
  - `generateDailyReport(String productCategory, String keywords, Integer daysRange)` - 支持动态参数
  - `getDefaultPrompt()` - 获取默认提示词

- **文件位置**：`ruoyi-price/src/main/java/com/ec/price/service/impl/IntelligenceReportServiceImpl.java`
- **关键技术点**：
  - 使用 RestTemplate 调用 Coze API
  - SSE 流式响应解析（正则表达式匹配 data: {...} 格式）
  - 从 workflow_end 事件中提取 output.daily_report 字段
  - 支持参数动态传递（产品类别、关键词、时间范围）

#### 2. 创建 Controller
- **文件位置**：`ruoyi-price/src/main/java/com/ec/price/controller/IntelligenceReportController.java`
- **API 接口**：
  - GET /price/intelligence/report - 生成日报
    - 参数：productCategory（可选）、keywords（可选）、daysRange（可选）
  - GET /price/intelligence/default-prompt - 获取默认提示词

---

### 第二步：前端 (Vue) 开发 ✅

#### 1. API 封装
- **文件位置**：`ruoyi-ui/src/api/price/intelligence.js`
- **方法**：
  - getDailyReportStream(params) - 流式调用接口（timeout: 120s）
  - getDailyReport(prompt) - 普通调用接口
  - getDefaultPrompt() - 获取默认提示词

#### 2. 全局悬浮组件
- **文件位置**：`ruoyi-ui/src/components/IntelligenceReport.vue`
- **核心功能**：
  - 右下角悬浮按钮（固定定位，z-index: 9999）
  - 弹窗式参数配置界面
  - 产品类别下拉框（数据来源于 EcCategory）
  - 关键词输入框（逗号分隔）
  - 时间范围选择器（1-30 天）
  - 流式文本显示（逐字动画，30ms/字符）
  - Markdown 渲染（marked 库）

#### 3. 依赖安装
```bash
npm install --save marked
```

---

### 第三步：系统集成 ✅

#### 1. 全局注册
- **文件位置**：`ruoyi-ui/src/App.vue`
- 将 IntelligenceFloat 组件注册为全局组件

#### 2. 移除看板集成
- **文件位置**：`ruoyi-ui/src/views/dashboard.vue`
- 从首页移除自动加载的日报组件，改为按需触发

---

## 技术亮点

### 1. SSE 流式响应处理
```java
// 解析 SSE 事件流，提取 workflow_end 中的 daily_report
private String parseStreamResponse(String responseBody) {
    // 正则匹配 "data: {...}" 格式
    // 查找 workflow_end 事件
    // 提取 output.daily_report 字段
}
```

### 2. 动态参数传递
- 前端传入驼峰命名参数（productCategory、keywords、daysRange）
- 后端直接接收并传递给 Coze API
- 支持空值兜底（使用默认值）

### 3. 流式显示效果
- 逐字显示动画（模拟打字机效果）
- Markdown 实时渲染
- 代码块深色主题样式

### 4. UI/UX优化
- 悬浮按钮始终可见（fixed 定位）
- 弹窗内下拉框层级修复（popper-append-to-body="false"）
- 响应式布局适配

---

## 注意事项

### 1. Token 安全
- API Token 硬编码在后端 Service 中（生产环境应移至配置文件）
- 前端不暴露任何敏感信息

### 2. 跨域处理
- Controller 添加 @CrossOrigin 注解
- 支持前后端不同端口访问

### 3. 超时设置
- 前端 timeout: 120000ms（2 分钟）
- 后端 RestTemplate 默认超时时间充足

### 4. 分类数据源
- 产品类别下拉框严格绑定 EcCategory 实体
- 确保前后端分类数据一致性

---

## 运行步骤

### 1. 后端启动
```bash
# 在项目根目录执行
ry.bat
```

### 2. 前端启动
```bash
cd ruoyi-ui
npm run dev
```

### 3. 使用说明
1. 打开系统任意页面
2. 点击右下角蓝色圆形悬浮按钮
3. 选择产品类别、输入关键词、设置时间范围
4. 点击"生成日报"按钮
5. 等待 AI 生成（约 3-5 秒）
6. 查看流式显示的日报内容
