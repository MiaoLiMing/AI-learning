# 第 1 周：Python + FastAPI + LLM API

## 本周目标

做出一个最小可用的 AI 聊天后端 API。

## 学习内容

- Python 基础语法
- 虚拟环境
- FastAPI 基础
- Pydantic 请求/响应模型
- 调用 DeepSeek / Qwen / OpenAI-compatible API
- `.env` 配置
- 基础错误处理

## 具体任务

### Day 1

- [ ] 安装 Python 3.11+
- [ ] 创建 `ai-chat-api` 项目
- [ ] 配置虚拟环境
- [ ] 学习 `pip` / `venv` / `requirements.txt`

### Day 2

- [ ] 创建 FastAPI 服务
- [ ] 实现 `/health` 接口
- [ ] 实现 `/api/chat` 空接口
- [ ] 使用 Swagger 测试接口

### Day 3

- [ ] 注册 DeepSeek 或 Qwen API
- [ ] 用 Python 请求模型 API
- [ ] 把 API Key 放到 `.env`
- [ ] 封装 `LLMClient`

### Day 4

- [ ] 实现单轮对话
- [ ] 支持 system prompt
- [ ] 支持 temperature 参数
- [ ] 增加异常处理

### Day 5

- [ ] 实现多轮对话消息结构
- [ ] 保存临时对话历史
- [ ] 记录请求耗时
- [ ] 整理 README

### 周末

- [ ] 重构目录结构
- [ ] 补充接口文档
- [ ] 用 Postman / Apifox 测试
- [ ] 录制一次项目演示或截图

## 产出物

- 一个 FastAPI AI 聊天后端
- README
- 接口示例

## 验收标准

- [ ] `uvicorn` 能启动服务
- [ ] `/health` 返回正常
- [ ] `/api/chat` 能返回模型回答
- [ ] API Key 不写死在代码里
- [ ] README 说明如何运行

## 本周不要做

- 不要做复杂前端
- 不要研究模型训练
- 不要接多个模型供应商
