# 第 7 周：LangGraph 多步骤 Agent

## 本周目标

用 LangGraph 做出多步骤、可追踪的 Agent 工作流。

## 学习内容

- Agent 状态
- Graph / Node / Edge
- 条件分支
- 工具节点
- 多轮执行
- 状态持久化

## 具体任务

### Day 1

- [ ] 学习 LangGraph 基础概念
- [ ] 跑通一个最小 Graph
- [ ] 理解 State 的作用

### Day 2

- [ ] 把 Function Calling 工具接入 LangGraph
- [ ] 实现“模型节点 -> 工具节点 -> 模型节点”
- [ ] 输出执行轨迹

### Day 3

- [ ] 实现业务场景：销售助理 Agent
- [ ] 步骤：理解需求、查客户、查订单、生成邮件
- [ ] 保存每一步结果

### Day 4

- [ ] 增加条件分支
- [ ] 无客户时提示补充信息
- [ ] 有风险操作时进入确认节点

### Day 5

- [ ] 前端展示 Agent 执行步骤
- [ ] 展示每一步状态
- [ ] 展示失败原因

### 周末

- [ ] 整理 Agent 架构图
- [ ] 对比普通 Chatbot 与 Agent
- [ ] 写 README

## 产出物

- 多步骤业务 Agent

## 验收标准

- [ ] Agent 能连续调用多个工具
- [ ] 前端能看到执行轨迹
- [ ] 每一步有状态和日志

## 面试准备

你要能讲清楚：

- 为什么 Agent 需要状态
- 为什么复杂 Agent 需要工作流
- LangGraph 解决什么问题
