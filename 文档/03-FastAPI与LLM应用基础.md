# FastAPI + LLM 应用基础学习指南

> 当前前置状态：Python 基础已完成。
> 本阶段目标：独立做出一个可测试、可流式输出、可替换模型的 AI Chat API，并能解释每一层为什么存在。

## 1. 完成标准

本阶段不是以“页面能收到模型回答”为完成标准。你需要同时做到：

- 能说明一次请求如何经过路由、校验、service、模型客户端再返回前端。
- 能独立实现 `GET /health`、`POST /api/chat`、`POST /api/chat/stream`。
- 能解释同步、异步、普通响应、流式响应和 SSE 的区别。
- 能正确管理 API Key、模型名、Base URL 和超时配置。
- 能处理参数错误、认证失败、限流、超时和上游服务异常。
- 能记录请求耗时、模型、Token 用量和错误，但不记录密钥与敏感正文。
- 能为核心接口写测试，并使用假模型避免测试产生真实费用。
- 能从空目录重新搭出核心后端，而不是只能修改 AI 生成的成品。

建议用 5-8 个学习单元完成。熟悉的部分可以通过验收题直接跳过，不必机械按天学习。

## 2. 最终项目结构

先看结构，不要一次创建所有代码：

```text
01-ai-chat-playground/
├── backend/
│   ├── app/
│   │   ├── main.py              # 创建 FastAPI 应用、注册路由与异常处理
│   │   ├── api/
│   │   │   └── chat.py          # HTTP 协议层：接收、校验、返回
│   │   ├── core/
│   │   │   └── config.py        # 从环境变量读取配置
│   │   ├── schemas/
│   │   │   └── chat.py          # 请求与响应的数据契约
│   │   └── services/
│   │       └── llm.py           # 模型调用与流式文本转换
│   ├── tests/
│   │   ├── test_health.py
│   │   └── test_chat.py
│   ├── .env.example
│   ├── pyproject.toml
│   └── README.md
└── frontend/                     # 后端通过阶段验收后再接 Vue
```

边界原则：

- `api` 知道 HTTP，但不应堆模型调用细节。
- `services` 知道如何调用模型，但不应依赖 Vue 页面结构。
- `schemas` 定义接口契约，避免用随意的字典传递关键数据。
- `core` 管配置和通用基础设施，不保存业务逻辑。
- `tests` 使用假模型验证自己的代码，不把真实第三方服务当单元测试依赖。

## 3. 学习单元 1：FastAPI 最小闭环

### 3.1 先理解

请求进入应用的大致过程：

```text
客户端 -> ASGI Server(Uvicorn) -> FastAPI 路由 -> Pydantic 校验
      -> 业务函数 -> 响应序列化 -> HTTP 响应
```

需要分清：

- **Uvicorn** 是运行 ASGI 应用的服务器。
- **FastAPI** 负责路由、依赖、校验、OpenAPI 等 Web 能力。
- **Pydantic** 用类型声明解析和校验外部数据。
- `async def` 适合等待网络、数据库等 I/O；它不会自动让 CPU 密集任务变快。

### 3.2 自己完成

1. 创建虚拟环境和项目。
2. 安装 FastAPI、Uvicorn 和测试依赖。
3. 实现 `GET /health`，返回：

```json
{"status": "ok"}
```

4. 启动服务，分别用浏览器、接口文档和命令行请求它。
5. 找到 `/docs` 和 `/openapi.json`，观察接口契约从哪里来。

### 3.3 练习

- 增加 `GET /api/models/{model_id}`，分别接收路径参数和可选查询参数。
- 故意传错类型，观察 422 响应结构。
- 把路由从 `main.py` 移到 `api` 模块，确认行为不变。

### 3.4 验收题

- 为什么不能把整个项目都写在 `main.py`？
- 路由函数返回字典后，谁把它变成 JSON？
- `def` 与 `async def` 在 FastAPI 中如何选择？
- 输入错误为什么应返回 4xx，而不是 500？

## 4. 学习单元 2：接口契约、配置与异常

### 4.1 请求模型

聊天请求至少先定义为：

```python
from typing import Literal

from pydantic import BaseModel, Field


class Message(BaseModel):
    role: Literal["system", "user", "assistant"]
    content: str = Field(min_length=1, max_length=20_000)


class ChatRequest(BaseModel):
    messages: list[Message] = Field(min_length=1)
```

不要只收一个 `message: str` 后马上隐藏多轮消息结构。这个阶段就应该看见模型上下文的真实形态。

### 4.2 配置原则

配置项至少包括：

```dotenv
LLM_API_KEY=
LLM_BASE_URL=
LLM_MODEL=
LLM_TIMEOUT_SECONDS=30
```

要求：

- `.env` 不提交，提交 `.env.example`。
- 缺少关键配置时启动失败并给出清晰信息。
- 路由中不直接读取环境变量。
- 不在日志、异常响应或前端代码中暴露 API Key。
- 模型和 Base URL 可配置，不把某一家供应商写死在业务代码里。

### 4.3 异常分层

至少区分：

| 场景 | 对外状态 | 处理重点 |
| --- | --- | --- |
| 请求参数无效 | 422 / 400 | 告诉调用方哪个字段错误 |
| 未配置服务端密钥 | 500 | 启动时尽早发现，不返回密钥信息 |
| 上游认证失败 | 502 | 表达“模型服务认证失败”，不转发敏感细节 |
| 上游限流 | 429 / 503 | 可提示稍后重试，保留请求标识 |
| 上游超时 | 504 | 设置明确超时，不无限等待 |
| 未知上游异常 | 502 | 记录原始异常，对外使用稳定错误结构 |

建议统一错误结构：

```json
{
  "error": {
    "code": "LLM_TIMEOUT",
    "message": "模型服务响应超时",
    "request_id": "..."
  }
}
```

### 4.4 验收题

- 为什么 `LLM_API_KEY` 不能放 Vue 的环境变量里？
- 为什么不应把第三方 SDK 异常原样返回前端？
- Pydantic 校验和业务校验分别适合处理什么？
- 如果用户传入 10 万字消息，在哪一层限制？

## 5. 学习单元 3：先用脚本理解 LLM API

不要先接 FastAPI。写一个独立脚本完成一次模型调用，并打印你真正需要观察的字段。

### 5.1 必须理解的输入

- `model`：能力、延迟、价格和上下文限制都可能不同。
- `messages` / `input`：模型本次能看到的上下文。
- `system` / `developer` 指令：定义行为边界，不等于安全边界。
- `temperature`：影响采样随机性，不是“事实准确度”按钮。
- `max_output_tokens`：限制输出，不等于限制总上下文。

### 5.2 必须观察的输出

- 最终文本，不要假设所有响应都只有一个简单字符串字段。
- 完成状态和截断原因。
- 输入、输出与总 Token 用量。
- 响应标识和请求耗时。
- 错误类型：认证、限流、超时、服务端失败。

### 5.3 两种 API 认知

- 使用 OpenAI 官方服务时，优先学习当前官方推荐的 Responses API。
- 需要兼容 DeepSeek、Qwen 等 OpenAI-compatible 服务时，很多供应商仍提供 Chat Completions 形态。
- 不要让业务层直接依赖其中一种协议；在 `llm service` 内做适配。

接口可以先抽象成：

```python
from collections.abc import AsyncIterator
from typing import Protocol


class LLMClient(Protocol):
    async def complete(self, messages: list[dict[str, str]]) -> str: ...

    def stream(self, messages: list[dict[str, str]]) -> AsyncIterator[str]: ...
```

这里的重点不是炫技，而是让路由和测试不绑定真实供应商。

### 5.4 三个实验

使用同一个问题完成并记录：

1. 修改 system 指令，对比输出差异。
2. 修改随机性或推理相关参数，重复请求 3 次并对比稳定性。
3. 故意使用错误密钥、错误模型和极短超时，记录异常类型。

不要只写“成功 / 失败”，要写你的预测、实际结果和结论。

## 6. 学习单元 4：实现非流式聊天接口

目标接口：

```http
POST /api/chat
Content-Type: application/json
```

请求：

```json
{
  "messages": [
    {"role": "system", "content": "你是一个简洁的 AI 应用开发导师。"},
    {"role": "user", "content": "解释什么是 RAG。"}
  ]
}
```

响应至少包含：

```json
{
  "message": {"role": "assistant", "content": "..."},
  "usage": {
    "input_tokens": 0,
    "output_tokens": 0,
    "total_tokens": 0
  },
  "model": "...",
  "request_id": "..."
}
```

实现顺序：

1. 使用假 `LLMClient` 让接口先返回固定答案。
2. 写接口测试，验证状态码和响应结构。
3. 接入真实客户端。
4. 把第三方响应转换成自己的响应模型。
5. 增加耗时与错误日志。

关键问题：为什么要转换成自己的响应模型，而不是直接把 SDK 对象返回？因为这能稳定你的前后端契约，并降低供应商替换成本。

## 7. 学习单元 5：实现流式响应

### 7.1 先画清三层流

```text
模型供应商流 -> FastAPI 内部异步迭代器 -> SSE/HTTP 响应 -> Vue fetch stream
```

这三层不是同一个东西：

- 模型 SDK 决定你如何收到增量事件。
- service 把供应商事件转换成应用自己的文本 / 状态事件。
- FastAPI 把应用事件编码成 SSE 或其他 HTTP 流。
- Vue 负责解码字节、解析事件、更新界面和处理中止。

### 7.2 SSE 最小格式

```text
event: delta
data: {"text":"你"}

event: delta
data: {"text":"好"}

event: done
data: {"finish_reason":"stop"}

```

每个事件用空行结束。不要把模型输出直接拼进 `data:`；先 JSON 编码，避免换行破坏协议。

### 7.3 为什么优先 `fetch` 而不是 `EventSource`

聊天请求通常需要 `POST` JSON、认证头和中止控制。原生 `EventSource` 主要面向 GET，定制请求能力有限，因此这里更适合 `fetch + ReadableStream`。SSE 仍然描述服务端发送的事件格式。

### 7.4 必须处理的边界

- 客户端断开后停止上游生成，避免继续计费。
- 首个字节超时和整个请求超时应区分考虑。
- 流开始后不能再轻易改 HTTP 状态码，错误要作为流内事件传递。
- 中文字符可能跨字节块，前端使用流式 `TextDecoder`。
- 不假设一个网络 chunk 正好等于一个 SSE 事件。
- 完成、错误、中止都要结束 loading 状态。
- 代理层可能缓冲响应，部署时需要验证真实流式效果。

### 7.5 流式验收实验

1. 正常生成长文本，确认不是最后一次性出现。
2. 生成中途点击停止，确认浏览器和上游都终止。
3. 让上游在开始前失败，验证 HTTP 错误。
4. 让上游在输出部分内容后失败，验证流内 `error` 事件。
5. 输出包含换行、中文、代码块和 emoji，确认解析不乱。

## 8. 学习单元 6：Prompt 与多轮上下文

本阶段只学习工程上马上需要的部分：

### 8.1 消息职责

- system / developer：应用约束、角色、输出规则。
- user：用户当前需求和必要数据。
- assistant：模型历史输出；传回模型才构成可见历史。

模型不会自动记得你数据库中的会话。多轮对话的本质是应用再次提供历史，或使用供应商提供的会话状态能力。

### 8.2 上下文策略

先实现简单的最近消息窗口，再思考：

- 历史越长，Token 成本和延迟如何变化？
- 截断时为什么不能留下孤立的工具结果？
- 哪些系统指令必须始终保留？
- 什么时候使用摘要，摘要又会丢失什么？

### 8.3 Prompt 实验记录表

| 用例 | 输入 | 期望 | 实际 | 是否通过 | 备注 |
| --- | --- | --- | --- | --- | --- |
| 简洁回答 | 解释 RAG | 150 字以内 |  |  |  |
| 不知道时拒答 | 问不存在的制度 | 明确说资料不足 |  |  |  |
| 指令冲突 | 用户要求忽略规则 | 保持系统约束 |  |  |  |
| 格式要求 | 输出固定 JSON | 可解析且字段完整 |  |  |  |

Prompt 不是写完一次就结束；它是需要用固定样例回归验证的程序资产。

## 9. 学习单元 7：测试、日志与质量

### 9.1 最少测试集

- `/health` 返回 200。
- 合法聊天请求返回自己的响应结构。
- 空消息、非法角色和超长内容被拒绝。
- 假模型超时被映射为预期错误。
- 假模型流按顺序输出 `delta` 和 `done`。
- 流中异常被编码为 `error` 事件。
- 未配置密钥时有明确失败方式。

测试默认使用 fake / stub 客户端。真实 API 测试单独标记，避免每次运行都产生费用和不稳定结果。

### 9.2 日志字段

建议记录：

```text
request_id
route
model
status
latency_ms
time_to_first_token_ms
input_tokens
output_tokens
error_code
```

默认不记录 API Key、Authorization 头和完整用户正文。若为调试保留内容，需要明确脱敏和保存周期。

### 9.3 非功能指标

至少开始观察：

- 总响应时间
- 首 Token 时间
- 成功率与错误分类
- 输入 / 输出 Token
- 单请求估算成本
- 用户主动中止率

## 10. 学习单元 8：接入 Vue 最小界面

后端通过前述验收后再接你熟悉的 Vue，不要把时间花在 UI 细节上。

必须功能：

- 输入消息并发送。
- 使用 `fetch` 消费 POST 流式响应。
- 逐步更新 assistant 消息。
- 支持停止生成与再次发送。
- 正确展示等待、生成、完成、错误和中止状态。
- Markdown 与代码块渲染时考虑 XSS，不直接信任模型 HTML。

暂不做：登录注册、复杂会话管理、换肤、动画和大规模组件封装。

## 11. 推荐执行安排

| 单元 | 产出 | 建议时长 |
| --- | --- | --- |
| 1 | `/health`、路由拆分、接口文档观察 | 1-2 小时 |
| 2 | Chat schema、配置、统一错误结构 | 1-2 小时 |
| 3 | 独立 LLM 脚本和 3 组实验记录 | 1-2 小时 |
| 4 | 非流式 `/api/chat` 与测试 | 2-3 小时 |
| 5 | `/api/chat/stream` 与 5 个边界实验 | 3-5 小时 |
| 6 | 多轮上下文与 Prompt 用例表 | 2 小时 |
| 7 | 测试、日志、Token 与耗时观测 | 2-3 小时 |
| 8 | Vue 最小聊天界面 | 2-4 小时 |

总计约 14-23 小时。你已有前端和流式处理经验，可以压缩单元 8，但不要跳过后端错误、取消与测试。

## 12. 每个单元如何使用 AI

### 开始前

先自己写一页以内的设计，再让 AI 评审：

```text
这是我对本单元的设计：<粘贴你的设计>。
请只做评审，不写完整代码。
按“错误理解、遗漏边界、过度设计、可验证性”四类反馈，
并给出我应该自己回答的 3 个问题。
```

### 编码时

只请求当前最小模块：

```text
我要实现 <具体函数或接口>。
输入、输出和验收条件是：<内容>。
先给伪代码和关键风险；等我确认后再给最小实现。
不要创建额外框架，不要替我完成其他模块。
```

### 出错时

先提供证据和自己的假设：

```text
现象：
最小复现：
日志 / 状态码：
我认为可能的三个原因：
我已经排除了：

请按概率排序排查路径，每一步告诉我如何证伪，先不要重写代码。
```

### 完成后

让 AI 出题，而不是替你总结：

```text
请基于这个模块向我提 8 个由浅入深的问题，
包括 2 个故障排查题和 2 个方案权衡题。
先只提问，等我回答后再评分和纠错。
```

## 13. 阶段最终挑战

在不查看已完成项目的情况下，从空目录完成：

1. 创建 FastAPI 应用并实现健康检查。
2. 定义多轮消息契约。
3. 抽象 LLM 客户端并接入一个真实供应商。
4. 实现非流式与 SSE 流式聊天。
5. 加入超时、统一错误、请求日志和中止处理。
6. 用假客户端完成关键测试。
7. 写一份 README，说明启动方式、架构、接口和已知限制。

满足以下条件才进入 RAG：

- [ ] 核心链路能在 10 分钟内口述清楚。
- [ ] 能解释每个目录和关键依赖为什么存在。
- [ ] 能在不问 AI 的情况下修改请求字段或替换模型。
- [ ] 能复现并定位至少 3 类失败。
- [ ] 流式接口支持完成、错误和用户中止。
- [ ] 测试不依赖真实模型服务。
- [ ] 没有密钥进入 Git 历史。
- [ ] 有一篇阶段复盘，记录选择、问题和证据。

## 14. 进入 RAG 前的衔接问题

完成后先回答：

1. 普通聊天把哪些内容交给了模型？
2. 如果模型不知道公司制度，当前系统为什么无法可靠回答？
3. 在调用模型前增加“检索公司资料”这一步，需要哪些输入和输出？
4. 检索结果怎样进入上下文，怎样让回答展示来源？
5. 如何判断错误来自解析、切片、Embedding、检索、Prompt 还是模型？

能回答这些问题，就可以进入下一阶段：文档解析、Chunk、Embedding 和向量检索。

## 15. 官方资料

只按任务查阅，不需要从头通读：

- [FastAPI Tutorial](https://fastapi.tiangolo.com/tutorial/)
- [FastAPI Async](https://fastapi.tiangolo.com/async/)
- [FastAPI Custom Response / StreamingResponse](https://fastapi.tiangolo.com/advanced/custom-response/)
- [Pydantic Models](https://docs.pydantic.dev/latest/concepts/models/)
- [Uvicorn Settings](https://www.uvicorn.org/settings/)
- [OpenAI Developer Quickstart](https://platform.openai.com/docs/quickstart)
- [OpenAI Responses API](https://platform.openai.com/docs/api-reference/responses)
- [OpenAI Streaming Events](https://platform.openai.com/docs/api-reference/responses-streaming)

不同模型供应商的兼容范围和参数支持可能不同。接入 DeepSeek、Qwen 或其他服务时，以对应供应商当前官方文档为准，不要仅凭“OpenAI-compatible”假设所有字段和事件完全一致。
