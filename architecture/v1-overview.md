# V1 架构说明：File-Based Memory

## 目录结构

```
~/.claude/projects/<project-hash>/memory/
├── MEMORY.md                    ← 索引，每次对话必读
├── feedback_negotiation.md
├── feedback_research_first.md
├── feedback_split_adjustment.md
├── feedback_trading_discipline.md
├── feedback_unicode_paths.md
├── feedback_update_md.md
├── project_*.md                 ← 多个项目文件
├── user_background.md
└── user_persona.md
```

## 文件格式规范

所有记忆文件使用统一的 frontmatter 格式：

```markdown
---
name: 短横线连接的唯一标识
description: 一句话摘要（MEMORY.md 索引使用这行决定是否加载）
metadata:
  type: user | feedback | project | reference
---

正文内容

**Why:** 为什么存这条记忆（来源事件）

**How to apply:** 未来对话中何时、如何使用这条规则
```

## MEMORY.md 格式规范

```markdown
# Memory Index

- [记忆标题](文件名.md) — 一句话摘要（<150字符）
```

**关键限制：** MEMORY.md 超过 200 行后内容会被截断。保持索引简洁，详情放各自文件。

## 四类记忆的写法差异

### feedback 类（最重要）

记录 AI 行为被纠正的规则，**必须包含 Why 和 How to apply**：

```markdown
规则本身（一句话，直接可执行）

**Why:** 触发这条规则的具体事件（有日期最好）

**How to apply:** 在哪些场景下激活这条规则，边界条件是什么
```

### project 类

记录进行中的项目状态，**信息衰减快，需要频繁更新**：

```markdown
项目核心事实（当前状态、关键路径、下一步）

**Why:** 项目背景和目标

**How to apply:** 继续这个项目时直接从这里读取上下文，不用重新解释
```

### user 类

记录用户背景，**写一次，长期有效**：
- 技术水平、工具偏好、工作方式
- 不写具体项目进展（那是 project 类的事）

### reference 类

记录外部资源位置：
- 文档 URL
- 内部文件路径
- 工具/服务的访问方式

## 运作机制

1. Claude Code 启动每次对话时，**自动读取 MEMORY.md**
2. 根据 MEMORY.md 的描述判断哪些文件与当前任务相关，**按需读取详情文件**
3. 对话结束时，AI 判断是否有新信息需要写入记忆文件
4. 用户可以说"记住这件事"触发立即写入

## V1 的已知局限

| 问题 | 影响 | V2 的解法 |
|------|------|----------|
| 无语义检索 | 靠关键词匹配，相关文件可能漏读 | 引入向量检索（sqlite-vec）|
| 无自动压缩 | 长项目后 token 消耗上升 | Mermaid 短期记忆压缩 |
| 无分层追溯 | 结论无法追溯到原始证据 | L0-L3 分层架构 |
| 手动维护 | 需要人工判断何时更新 | 自动触发更新机制 |
