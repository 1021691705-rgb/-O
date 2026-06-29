# Agent Skill 资源索引

## 一句话总结

这是从小红书笔记《AI生产力核弹：4组顶级Skill重构Agent工作流》中延伸出来的 Skill 资源清单，用来记录可复用的 Agent Skill、MCP 和工作流框架，方便后续建设 AI设计公司总控系统。

## 来源

- 关联笔记：[[AI生产力核弹：4组顶级Skill重构Agent工作流]]
- 整理日期：2026-06-29
- 整理方式：基于截图中出现的 Skill 名称逐项检索公开来源。部分名称不是唯一项目，当前只记录较可信的对应来源。

## 分类说明

这些资源不全是同一种“Skill”：

- 官方 Skill：由 Anthropic 等官方仓库维护，可直接作为 Skill 参考。
- 社区 Skill：个人或社区维护，需要先审查内容再使用。
- MCP / 插件：不是 Skill 本体，但能给 Agent 增加外部工具或上下文能力。
- 工作流框架：更像一套 Agent 使用规范、命令体系或虚拟团队流程。

## 资源清单

| 名称 | 类型 | 主要用途 | 来源 |
|---|---|---|---|
| Skill Creator | 官方 Skill | 创建和改进 Skill | https://github.com/anthropics/skills |
| Agent Skill Creator | 社区 Skill | 把工作流生成可复用 Skill | https://github.com/FrancyJGLisboa/agent-skill-creator |
| Skill Manager | 社区工具 | 管理、安装、组织 Skill | https://github.com/ashimoon/claude-skill-manager |
| Find Skills | 社区 / Vercel Skill | 搜索和发现可用 Skill | https://github.com/vercel-labs/skills |
| Superpowers | 工作流框架 | 给 Claude Code、Codex 等增加工程工作流 | https://github.com/obra/superpowers |
| gstack | 工作流框架 | Garry Tan 的 Claude Code 虚拟工程团队 / 工作流 | https://github.com/garrytan/gstack |
| Context7 | MCP / Skill 生态 | 给 Agent 提供最新文档上下文 | https://github.com/upstash/context7 |
| GitMCP | MCP | 让 Agent 读取 GitHub 仓库上下文 | https://github.com/idosal/git-mcp |
| Matt's Skills | 社区 Skill 集合 | 工程、前端、代码工作流 Skill | https://github.com/mattpocock/skills |
| Frontend Design | 官方 Skill | 前端视觉设计和 UI 质量提升 | https://github.com/anthropics/skills/tree/main/skills/frontend-design |
| UI/UX Pro Max | 社区 Skill | 专业 UI/UX 设计知识库 | https://github.com/nextlevelbuilder/ui-ux-pro-max-skill |
| Xiaohongshu Image Skill | 社区内容 Skill | 生成小红书图文卡片 | https://github.com/JimLiu/baoyu-skills |
| Infographic Skill | 社区内容 Skill | 生成信息图 | https://github.com/JimLiu/baoyu-skills |
| Cover Image Skill | 社区内容 Skill | 生成文章封面 | https://github.com/JimLiu/baoyu-skills |
| Markdown to HTML Skill | 社区内容 Skill | Markdown 转公众号友好 HTML | https://github.com/JimLiu/baoyu-skills |
| Translation Skill | 社区内容 Skill | 翻译内容 | https://github.com/JimLiu/baoyu-skills |
| Publish WeChat Skill | 社区发布 Skill | 发布微信公众号 | https://github.com/JimLiu/baoyu-skills |
| Publish Weibo Skill | 社区发布 Skill | 发布微博 | https://github.com/JimLiu/baoyu-skills |

## 对 AI设计公司总控的价值

### 1. 元能力

优先关注：

- `Skill Creator`
- `Agent Skill Creator`
- `Skill Manager`
- `Find Skills`

价值：

- 把项目复盘、岗位 SOP、质检标准沉淀成可复用 Skill。
- 让每个岗位不再依赖临时提示词。
- 降低后续新员工 / 新 Agent 的配置成本。

### 2. 工程与上下文能力

优先关注：

- `Superpowers`
- `gstack`
- `Context7`
- `GitMCP`
- `Matt's Skills`

价值：

- 参考它们的工作流组织方式，建设自己的 AI设计公司虚拟岗位协同。
- 用 Context7 / GitMCP 这类工具补足“最新文档”和“仓库上下文”能力。
- 借鉴 gstack / Superpowers 的任务拆解、代码审查、交付闭环方法。

### 3. 设计与内容生产能力

优先关注：

- `Frontend Design`
- `UI/UX Pro Max`
- `Xiaohongshu Image Skill`
- `Infographic Skill`
- `Cover Image Skill`
- `Markdown to HTML Skill`
- `Translation Skill`
- `Publish WeChat Skill`
- `Publish Weibo Skill`

价值：

- 形成从知识入库到内容发布的流水线。
- 把 Obsidian 笔记转成小红书、公众号、信息图、封面等多种内容资产。
- 支撑设计公司在营销内容、案例复盘、知识输出上的复用。

## 建议采用顺序

### 第一批：知识库和 Skill 生产

- Skill Creator
- Find Skills
- Agent Skill Creator
- Obsidian 入库相关自建 Skill

目标：

```text
用户发知识
-> 总结为 Obsidian 笔记
-> 提炼可复用 SOP
-> 转成 Skill
```

### 第二批：岗位协同

- Superpowers
- gstack
- Matt's Skills
- 自建项目经理 Skill
- 自建质检总监 Skill

目标：

```text
项目需求
-> 岗位拆解
-> 多岗位协同
-> 质检闭环
-> 项目复盘
```

### 第三批：内容分发

- Xiaohongshu Image Skill
- Infographic Skill
- Cover Image Skill
- Markdown to HTML Skill
- Publish WeChat Skill
- Publish Weibo Skill

目标：

```text
Obsidian 知识
-> 小红书图文
-> 公众号文章
-> 信息图 / 封面
-> 多平台发布
```

## 使用风险

- 社区 Skill 使用前必须看源码和 `SKILL.md`，不能直接信任。
- 发布类 Skill 可能需要账号权限或 token，不能把密钥写进知识库。
- MCP 工具会扩大 Agent 可访问范围，要明确权限边界。
- 内容生成类 Skill 必须符合品牌风格和平台规则。
- 外部 Skill 只能做参考，最终要沉淀成适合本公司岗位流程的内部 Skill。

## 待确认

- [ ] 是否需要把这些仓库逐个 clone 到本地研究。
- [ ] 是否需要挑 3 个最适合 AI设计公司的 Skill 先试用。
- [ ] 是否要建立 `30-资源/工具/Skill研究` 目录。
- [ ] 是否要把“知识入库 Skill”作为第一个自建 Skill。

## 相关链接

- [[AI生产力核弹：4组顶级Skill重构Agent工作流]]
- [[OpenClaw 企业微信多 Agent 教程分析]]
- [[飞书链接 Codex 成功经验]]
- [[资源总览]]
