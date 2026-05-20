# Agent Workflow 学习笔记

> 来源：https://aiweb3.school/zh/handbook/bridge/agent-workflow/
> 日期：2026-05-20

## 核心概念

Agent Workflow 是把"用户目标 → 上下文读取 → 计划生成 → 工具调用 → 风险检查 → 执行 → 记录和复盘"组织成可控流程，而不是让模型自由发挥。

**核心思想**：把概率模型放进确定性流程里。

## 六个知识节点

### 1. Task Graph（任务图）
- 把目标拆成节点和依赖
- 示例：swap 操作拆成 9 步
- 每步可设输入、输出、权限和停止条件

### 2. State Machine（状态机）
- 明确状态流转：draft → context_loaded → plan_ready → waiting_user_confirmation → submitted → confirmed/reverted/cancelled
- 价值：系统不会忘记自己在哪，不会重复危险动作

### 3. Human-in-the-loop
- 分层策略：只读→自动 / 草稿→自动 / 小额→session key / 高风险→人工 / 超出→拒绝
- 重点：人确认时能否看懂资产变化和风险

### 4. Retry / Fallback
- 不同失败类型不同处理
- 关键：交易 pending 不能简单重发；Fallback 不能用未评估模型继续发交易

### 5. Trace
- 每步完整记录：用户目标、模型版本、上下文来源、工具I/O、policy判断、模拟结果、人工确认、交易哈希、最终状态
- 用于复盘：模型理解错？工具返回错？policy 漏了？用户确认了错误动作？

### 6. Evaluation Harness
- 不只测回答质量，还测：拒绝越权、识别错误链/合约、缺数据时停止、避免危险 calldata
- Regression Set：固定用例防退化

## 与其他章节的关系

- Chain-aware Context → 提供事实（输入层）
- Web3 Tool Use → 提供能力（执行层）
- Agent Wallet → 提供权限边界
- Agent Workflow → 流程骨架，把以上组织成可执行但可控的路径

## 产品研究思考

- 没有 workflow 的项目 = "模型直接调用工具"，demo 够用，生产不够
- 值得追问：现有 AI+Web3 产品哪些有真正的 workflow，哪些只是模型接了个工具？

## 最小实践（TODO）

- [ ] 设计一个链上 Agent 工作流：解释并准备一笔小额 ERC-20 swap
- [ ] 画出 task graph
- [ ] 为每一步写输入、输出、可用工具和失败处理
- [ ] 标出 human-in-the-loop 步骤
- [ ] 写 5 个 regression case：正常、错误链、滑点过大、余额不足、用户拒绝

## 扩展阅读

- LangGraph Concepts：状态、控制流和持久化
- OpenAI Tools Guide：模型调用工具的输入输出组织
- OpenAI Agents SDK：trace、tool、handoff 和 guardrail
- Tenderly Simulations：链上执行前的交易模拟
- ERC-4337 Documentation：智能账户和 UserOperation
