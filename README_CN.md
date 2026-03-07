# 中文说明

## AgentMind 是什么？

给 AI 助手装一个"大脑操作系统"。两个核心模块：

**元认知协议** — 管"怎么思考"
- 接到任务先做三层思考（表面问题→真实需求→下一个问题）
- 得出结论后反向验证（主动找反例）
- 同一方法失败3次强制换路
- 防止假完成、编造数据、中途失忆

**持久记忆** — 管"记住什么"
- 四层记忆：工作记忆 / 情景记忆 / 语义记忆 / 程序记忆
- 全部存为本地 Markdown 文件，人类可读可编辑
- 新对话开始时读取记忆，任务结束时写入记忆
- 零依赖，不需要数据库，不需要 API Key

## 快速开始

```bash
git clone https://github.com/q7766206/AgentMind.git

# Claude Code 用户
cp -r AgentMind/metacognition ~/.claude/skills/agentmind-metacognition
cp -r AgentMind/memory ~/.claude/skills/agentmind-memory
mkdir -p ~/agentmind/memory
cp AgentMind/memory/templates/* ~/agentmind/

# 其他 AI 助手
# 将 SKILL.md 的内容加入你的系统提示词即可
```

## 和同类项目的区别

Mem0、MemOS 需要写代码调 API，面向开发者。AgentMind 是纯 Markdown 方案，复制文件就能用，面向所有 AI 用户。

而且 AgentMind 是唯一同时提供"思考协议 + 记忆系统"的项目。别的只管记忆，不管思考质量。
