# 第 1 周：Python + FastAPI + LLM API

## 本周目标

做出一个最小可用的 AI 聊天后端 API，并能解释 Python 后端、FastAPI 路由、模型 API 调用的基本流程。

## 能力目标

- 能创建 Python 虚拟环境并管理依赖。
- 能写 FastAPI 路由、请求模型、响应模型。
- 能调用 DeepSeek / Qwen / OpenAI-compatible API。
- 能解释一次 `/api/chat` 请求从前端到模型再返回的完整链路。
- 能看懂并修改 AI 生成的基础 Python 代码。

## 任务总览

| 时间    | 任务             | 产出                    | 记录重点                               |
| ----- | -------------- | --------------------- | ---------------------------------- |
| Day 1 | Python 环境与项目结构 | `ai-chat-api` 空项目     | venv、pip、目录结构                      |
| Day 2 | FastAPI 最小服务   | `/health`、`/api/chat` | 路由、Pydantic、Swagger                |
| Day 3 | 模型 API 调用      | `LLMClient`           | API Key、请求结构、错误处理                  |
| Day 4 | 单轮对话接口         | 可返回模型回答               | messages、system prompt、temperature |
| Day 5 | 多轮对话与日志        | 对话历史、耗时日志             | 上下文、日志、异常                          |
| 周末    | 整理与复盘          | README、接口示例           | 面试表达、问题清单                          |

## 每日任务卡

### Day 1：Python 环境与项目结构

任务：

- [ ] 安装 Python 3.11+
- [ ] 创建 `ai-chat-api`
- [ ] 创建虚拟环境
- [ ] 生成 `requirements.txt`
- [ ] 设计基础目录：`app/api`、`app/core`、`app/services`

知识点记录：

- Python 虚拟环境是什么：
- `pip` / `venv` / `requirements.txt` 的区别：
- 我选择的项目目录结构：
- 今天遇到的问题：
- 我能讲清楚的一句话：

### Day 2：FastAPI 最小服务

任务：

- [ ] 创建 FastAPI 应用
- [ ] 实现 `/health`
- [ ] 实现 `/api/chat` 空接口
- [ ] 使用 Swagger 测试

知识点记录：

- FastAPI 路由如何定义：
- Pydantic 请求模型的作用：
- Swagger 文档从哪里来：
- 今天遇到的问题：
- 我能讲清楚的一句话：

### Day 3：模型 API 调用

任务：

- [ ] 注册 DeepSeek 或 Qwen API
- [ ] 用 Python 发起一次模型请求
- [ ] 把 API Key 放到 `.env`
- [ ] 封装 `LLMClient`

知识点记录：

- Chat Completions 请求结构：
- `system` / `user` / `assistant` 的区别：
- 为什么 API Key 不能写死：
- 今天遇到的问题：
- 我能讲清楚的一句话：

### Day 4：单轮对话接口

任务：

- [ ] `/api/chat` 接入真实模型
- [ ] 支持 system prompt
- [ ] 支持 temperature
- [ ] 增加基础异常处理

知识点记录：

- temperature 控制什么：
- system prompt 的作用：
- 模型调用失败时应该返回什么：
- 今天遇到的问题：
- 我能讲清楚的一句话：

### Day 5：多轮对话与日志

任务：

- [ ] 实现多轮 messages 结构
- [ ] 临时保存对话历史
- [ ] 记录请求耗时
- [ ] 整理接口示例

知识点记录：

- 多轮对话为什么需要历史消息：
- 上下文太长会带来什么问题：
- 请求耗时日志有什么价值：
- 今天遇到的问题：
- 我能讲清楚的一句话：

### 周末：整理与复盘

任务：

- [ ] README 写清启动方式
- [ ] 写 3 个接口示例
- [ ] 用 Apifox / Postman 测试
- [ ] 画请求链路图

复盘记录：

- 本周最重要的 3 个知识点：
- 哪些代码是 AI 生成但我已经读懂：
- 哪些地方还解释不清：
- 下周要补的内容：

## 本周知识库

用自己的话整理：

- Python 虚拟环境：
- FastAPI：
- Pydantic：
- Chat Completions：
- `.env`：
- 异常处理：

## 面试表达

准备回答：

- 你如何用 FastAPI 封装一个大模型聊天接口？
- API Key 如何管理？
- 一次模型请求的完整链路是什么？
- 多轮对话为什么要传历史消息？

## 验收标准

- [ ] `uvicorn` 能启动服务
- [ ] `/health` 返回正常
- [ ] `/api/chat` 能返回模型回答
- [ ] API Key 不写死在代码里
- [ ] README 说明如何运行
- [ ] 能不用看稿讲清 `/api/chat` 的调用链路
