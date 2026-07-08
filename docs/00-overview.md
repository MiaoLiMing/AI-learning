# AI 应用开发转型整体规划

## 1. 转型定位

你的目标不应该是“AI 算法工程师”，而应该是：

> AI 应用全栈开发工程师 / 大模型应用开发工程师 / RAG & Agent 工程师

原因很简单：你已有 Vue、Node、TypeScript、前端工程和业务系统开发经验，这些能力在中小企业 AI 应用落地中非常值钱。企业缺的不是另一个会调模型 Demo 的人，而是能把 AI 能力接进业务系统、做成产品、部署上线、后续维护的人。

## 2. 目标岗位画像

中小企业常见 AI 应用岗位会要求你能做：

- 智能客服
- 企业知识库问答
- 文档解析与总结
- AI Copilot
- 业务流程自动化 Agent
- ChatBI / 数据问答
- AI 审核、AI 写作、AI 报表
- 内部工具和管理后台

核心能力组合：

```text
前端能力 + 后端 API + LLM API + RAG + Agent + 数据库 + 部署
```

## 3. 你的技术迁移路线

已有能力：

- Vue
- TypeScript
- Node.js
- 前端工程化
- 业务系统开发
- API 联调

需要补齐：

- Python 基础
- FastAPI
- 大模型 API 调用
- 流式输出
- Prompt Engineering
- Function Calling / Tool Calling
- RAG
- Embedding
- 向量数据库
- LangChain / LangGraph
- Docker 部署
- AI 系统日志、成本、评测

建议技术栈：

```text
前端：Vue 3 + TypeScript + Element Plus / Naive UI
后端：FastAPI + Python
数据库：PostgreSQL + pgvector
缓存：Redis
任务队列：Celery / Dramatiq / BullMQ
模型：DeepSeek / Qwen / OpenAI-compatible API
Agent：LangGraph
部署：Docker Compose
```

## 4. 学习原则

### 原则一：项目优先

不要先完整学完 Python、机器学习、深度学习再开始。你只需要围绕项目补知识：

- 项目需要接口，就学 FastAPI
- 项目需要知识库，就学 RAG
- 项目需要多步骤任务，就学 Agent
- 项目需要上线，就学 Docker

### 原则二：避开低性价比内容

短期内不要深挖：

- 大模型预训练
- Transformer 数学推导
- CUDA
- 分布式训练
- 论文复现
- 从零训练模型

这些适合算法岗，不适合你当前“快速胜任 AI 应用开发岗位”的目标。

### 原则三：每周必须有可展示成果

每周结束时，你都应该有一个能运行、能截图、能写进 README 的结果。

## 5. 12 周阶段划分

| 阶段 | 周期 | 目标 |
| --- | --- | --- |
| 入门启动 | 第 1-2 周 | 能调用模型，做出流式聊天系统 |
| RAG 主线 | 第 3-5 周 | 做出企业知识库问答系统 |
| Agent 主线 | 第 6-8 周 | 做出业务 Agent 自动化平台 |
| 工程化 | 第 9-10 周 | 完成权限、日志、成本、部署 |
| 求职准备 | 第 11-12 周 | 完成简历、项目包装、模拟面试 |

## 6. 最终验收标准

12 周后，你应该能做到：

- 独立开发 AI 聊天前端
- 独立开发 LLM 后端 API
- 独立接入 DeepSeek / Qwen / OpenAI-compatible API
- 独立实现文档上传、解析、向量化、检索、问答
- 独立实现基础 Agent 工具调用
- 独立完成 Docker Compose 部署
- 能讲清楚 RAG、Agent、Function Calling、流式输出、成本控制
- 简历中有 2 个完整 AI 应用项目

## 7. 每日节奏

工作日建议：

```text
20 分钟：读文档或看教程
40-60 分钟：写代码
10-20 分钟：记录问题和总结
```

周末建议：

```text
1 小时：复盘本周
3-4 小时：集中开发项目
1 小时：整理 README、截图、部署
```
