# 能力地图与岗位要求

## 1. 岗位关键词

投递和搜索岗位时重点关注：

- AI 应用开发工程师
- 大模型应用开发工程师
- AIGC 应用开发工程师
- LLM 应用开发工程师
- RAG 工程师
- Agent 开发工程师
- AI 全栈开发工程师
- 智能客服开发
- 知识库问答开发
- 企业 Copilot 开发

## 2. 必备能力

### 前端

你已有基础，但需要做出 AI 产品常见交互：

- 聊天窗口
- 流式输出
- Markdown 渲染
- 代码块渲染
- 文档上传
- 知识库管理
- 对话历史
- 引用来源展示
- Agent 执行步骤展示
- 工具调用日志展示

### 后端

建议从 Node 迁移到 Python + FastAPI，同时保留 Node 作为你的优势：

- REST API
- SSE / WebSocket
- 文件上传
- 后台任务
- 权限认证
- 数据库设计
- 日志记录
- 异常处理
- 限流

### 大模型 API

必须掌握：

- Chat Completions
- 流式响应
- System / User / Assistant 消息结构
- Temperature、max_tokens 等参数
- JSON 输出
- Function Calling / Tool Calling
- 模型切换
- 错误重试
- Token 成本统计

### RAG

必须掌握：

- 文档解析
- 文本清洗
- Chunk 切分
- Embedding
- 向量检索
- 关键词检索
- 混合检索
- Rerank
- 引用溯源
- 检索评估
- 幻觉控制

### Agent

必须掌握：

- 工具定义
- 工具调用
- 多步骤任务
- 状态管理
- 工作流编排
- 人工确认
- 失败重试
- 执行日志
- 权限控制

### 工程化

中小企业尤其看重：

- Docker Compose
- `.env` 配置
- PostgreSQL
- Redis
- 文件存储
- 日志
- 监控
- 成本统计
- 管理后台

## 3. 推荐学习顺序

优先级从高到低：

1. FastAPI
2. LLM API 调用
3. 流式输出
4. RAG
5. PostgreSQL + pgvector
6. LangGraph
7. Docker Compose
8. 权限、日志、成本统计
9. Prompt 评测

## 4. 暂时不学或少学

短期不建议投入大量时间：

- PyTorch 深度训练
- 模型预训练
- CUDA 优化
- 论文复现
- 自研向量数据库
- 自研 Agent 框架

## 5. 技术栈目标

12 周后建议你能在简历上写：

```text
熟悉 Vue3、TypeScript、Node.js，具备 AI 应用全栈开发能力；
熟悉 FastAPI、PostgreSQL、Redis、Docker；
掌握 LLM API 接入、流式输出、Function Calling、RAG、Agent 工作流；
能够独立开发企业知识库、智能客服、业务 Agent 等 AI 应用。
```
