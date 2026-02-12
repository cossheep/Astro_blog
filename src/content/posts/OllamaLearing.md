---
title: 私有化智能知识助手
published: 2025-01-21
description: 无需联网、数据不出本机，上传 PDF/Word 即可智能问答，保护隐私 + 零 API 成本
tags: [Project, Resume]
category: Resume
draft: false
---


## 一、项目经历：本地 AI 知识自动化入库系统（Python + Obsidian + Ollama）

> **项目目标**：构建一个完全本地运行的 AI 知识管理管道，实现“粘贴/PDF上传 → 大模型自动总结处理 → 结构化存入 Obsidian”的闭环，保障数据隐私的同时提升知识沉淀效率。

### 📌 核心亮点
- **100% 本地运行**：基于 Ollama + Qwen3 大模型，无需联网，数据不出本机  
- **多格式支持**：支持纯文本、Markdown、文字型 PDF、HTML 等常见知识载体  
- **智能结构化**：AI 自动生成标题、摘要、要点、自测问题与标签，符合认知科学原理  
- **无缝集成 Obsidian**：输出标准 Markdown 笔记，自动化生成相关笔记。
- **零依赖云服务**：规避 API 调用成本与隐私风险，适合敏感技术文档处理,利用本地大模型进行处理，输出文档。

### 🛠️ 技术栈
- **后端框架**：FastAPI（高性能异步 Web 框架）(Python实现)  
- **本地大模型**：Ollama + Qwen3:8B（中文能力强，支持 32K 上下文）  
- **系统集成**：Python tempfile（安全临时文件）、Pathlib（路径管理）  
- **知识管理**：Obsidian Vault（作为“第二大脑”存储结构化笔记）

### 📈 成果
- 实现 **单次点击** 将任意技术文档转化为可检索、可复习的 Obsidian 笔记  
- 支持每日快速摄入会议记录、论文片段、技术博客等内容，形成个人知识资产  
- 为后续扩展（如向量检索、自动关联已有笔记）奠定基础架构

---

## 二、实现步骤：从零到上线的完整流程

### 第 1 步：环境准备
1. 安装 [Ollama](https://ollama.com/)（Windows 版），确保命令行可用  
   ```bash
   ollama --version  # 验证安装成功
   ```
2. 拉取中文大模型  
   ```bash
   ollama pull qwen3:8b
   ```
3. 创建 Python 虚拟环境并安装依赖  
   ```bash
   pip install fastapi uvicorn pdfplumber beautifulsoup4 ollama python-multipart
   ```

### 第 2 步：设计系统架构
- 输入：用户粘贴文本 **或** 上传文件（.txt/.md/.pdf/.html）  
- 处理：  
  - 文件 → 提取纯文本  
  - 文本 → 调用 LLM 生成结构化内容（标题/摘要/要点/问题/标签）  
- 输出：生成带 YAML frontmatter 的 Markdown 文件，写入 Obsidian Vault

### 第 3 步：核心功能开发
1. **文件解析模块**  
   - 使用 `pdfplumber` 提取 PDF 文字  
   - 使用 `BeautifulSoup` 清洗 HTML 噪声（脚本、样式）  
   - 统一返回 ≤6000 字的干净文本

2. **AI 结构化模块**  
   - 设计 Prompt 强制 LLM 输出固定格式：  
     ```
     【标题】...【摘要】...【要点】...【问题】...【标签】...
     ```
   - 解析 LLM 响应，提取各字段，异常时返回错误标记

3. **文件名与内容生成**  
   - 用 LLM 生成的标题 + 时间戳 构建安全文件名（保留中文）  
   - 生成标准 Obsidian Markdown 格式，包含：  
     - YAML 元数据（创建时间、标签）  
     - 四段式结构（摘要/要点/问题/原文片段）

4. **Web 接口**  
   - FastAPI 提供 `/`（表单页）和 `/ingest`（提交接口）  
   - 支持 multipart/form-data，兼容文件与文本同时提交

### 第 4 步：调试与优化
- **修复常见问题**：  
  - “Unsupported file type” → 限制前端 accept 属性 + 后端校验  
  - “model not found” → 确认 Ollama 模型已 pull  
  - 文件名乱码 → 正则保留中文 `\u4e00-\u9fff`  
- **增强健壮性**：  
  - 添加 try-except 捕获文件读取/LLM 调用异常  
  - 空内容/空文本提前拦截，避免无效调用

### 第 5 步：部署与使用
1. 启动服务  
   ```bash
   uvicorn app:app --host 127.0.0.1 --port 8000
   ```
2. 访问 `http://127.0.0.1:8000`  
3. 粘贴一段技术文本或上传 PDF → 提交  
4. 查看 Obsidian Vault 中自动生成的结构化笔记 ✅

---

## 💡 项目价值总结

你不仅完成了一个工具，更构建了一套 **个人知识工业化流水线**：
- **输入**：碎片化信息（网页/PDF/笔记）  
- **处理**：AI 理解 + 结构化提炼  
- **输出**：可行动、可连接、可检索的知识单元  

这套系统未来可轻松扩展：
- 加入 ChromaDB 实现语义搜索  
- 通过 Obsidian 插件一键发送当前页面  
- 定时批量处理文献库

---
