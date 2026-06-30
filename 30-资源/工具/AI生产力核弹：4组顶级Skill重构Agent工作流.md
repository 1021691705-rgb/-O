# AI生产力核弹：4组顶级Skill重构Agent工作流

## 一句话总结

这篇小红书内容的核心观点是：Agent 的生产力不只取决于模型能力，更取决于 Skill 配置。把需求沉淀成可复用的 Skill，能让 Agent 从“临时理解任务”升级为“按稳定工作流交付结果”。

## 来源

- 平台：小红书
- 作者：截图显示为“小红书创作者”，具体账号待确认
- 标题：`AI生产力核弹：4组顶级Skill重构你的Agent工作流`
- 链接：https://www.xiaohongshu.com/discovery/item/69fe1512000000001e00ea0c?app_platform=android&ignoreEngage=true&app_version=9.3.2&share_from_user_hidden=true&xsec_source=app_share&type=video&xsec_token=CBfGT8C45BVWY5rYpqgsWqMa0tTbOoM5CdCDSWOHvHPRw=&author_share=1
- 收集日期：2026-06-29
- 整理依据：用户提供的长截图。网页端链接不可直接读取，当前笔记基于截图可见内容整理。

## 核心判断

截图里的判断可以概括成一句话：

> Skill 是 Agent 自我进化的燃料。

如果 Skill 没有配置好，Agent 使用起来会事倍功半；如果 Skill 结构清楚、职责明确、能复用，Agent 就能在设计、研发、运营和知识管理中形成稳定产出。

这和我们的 AI设计公司总控项目高度相关：

- 岗位 SOP 可以沉淀成 Skill。
- 设计流程可以沉淀成 Skill。
- 质检标准可以沉淀成 Skill。
- 降本增效方法可以沉淀成 Skill。
- Obsidian 知识库可以作为 Skill 的素材来源。

## 4 组 Skill 框架

### 1. 元 Skill：Agent 自我进化的燃料

截图提到的关键方向：

- `Skill Creator`
- `Skill Manager`
- `Skill Creator Agent`

作用：

- 把一次性经验沉淀成可复用 Skill。
- 帮 Agent 识别什么时候该调用哪个 Skill。
- 让工作流不断积累，而不是每次从零开始提示。

对我们的启发：

- 每次完成一个设计项目后，要把有效流程沉淀为 Skill。
- 每次质检发现高频问题，要转成质检 Skill。
- 每个岗位都应该有自己的岗位 Skill，而不是只靠通用聊天。

可落地成：

```text
设计项目复盘
-> 提炼稳定步骤
-> 写成 Obsidian SOP
-> 转成 Agent Skill
-> 下次项目自动调用
```

### 2. Find Skills：不要重复造轮子

截图中提到，Find Skills 用于寻找现成插件 / Skill 资源，并帮助 Agent 补齐能力。

作用：

- 先找已有能力，再决定是否自建。
- 避免每个需求都重新写一套。
- 降低搭建 Agent 工作流的时间成本。

对我们的启发：

- 做 AI设计公司时，不要每个岗位都从零写。
- 可先找已有技能：文案、详情页、图片质检、提示词、竞品分析、知识库整理。
- 找不到合适的，再由 `Skill Creator` 自建。

管理规则：

- 外部 Skill 只能作为参考，必须经过本公司流程适配。
- 涉及客户资料、品牌资产、私密知识时，不能直接交给未知外部工具。

### 3. 工作流编排：把责任传递给下一个岗位

截图中出现了几类偏工作流的 Skill：

- `Superpowers`
- `Context7`
- `GitMCP`
- `Matts Frontend Skill`

可读信息显示，这一组强调的是把任务拆成明确步骤，并在开发、前端、代码上下文、仓库资料之间建立协作。

作用：

- 让 Agent 不只是回答问题，而是能接入上下文、执行步骤、交接结果。
- 让岗位之间按固定顺序协作。
- 把“谁负责什么、输入是什么、输出是什么”固化下来。

对我们的启发：

AI设计公司可以照这个思路设计岗位链路：

```text
项目经理
-> 品牌资料管理员
-> 竞品分析员
-> 创意总监
-> 对应设计师
-> AI生图师
-> 质检总监
-> 流程优化经理
```

每个岗位都要明确：

- 输入资料
- 输出格式
- 完成标准
- 交接对象
- 质检规则

### 4. 内容创作：从生产到分发的整套系统

截图中提到的 Skill 包括：

- `Cover Image Skill`
- `Infographic Skill`
- `Xiaohongshu Image Skill`
- `Markdown to HTML Skill`
- `Translation Skill`
- `Publish WeChat / Publish Weibo Skill`

这一组偏内容生产和分发。

作用：

- 把一条内容从选题、图文、信息图、排版、翻译到发布串起来。
- 让 Agent 不是只写一段文案，而是负责完整传播链路。

对我们的启发：

我们的设计公司可以做一套“内容资产流水线”：

```text
原始知识
-> Obsidian 笔记
-> 小红书图文
-> 公众号长文
-> 海报 / 信息图
-> 多平台发布
-> 复盘数据
```

其中每个环节都可以是 Skill：

- 知识总结 Skill
- Obsidian 入库 Skill
- 小红书图文 Skill
- 信息图 Skill
- 公众号排版 Skill
- 多平台转写 Skill
- 发布复盘 Skill

## 对 AI设计公司总控的落地方案

### 第一阶段：先做知识入库 Skill

目标：

- 用户把知识发到群里。
- Agent 自动总结成 Obsidian Markdown。
- 标注来源、标签、关联笔记和待确认点。
- 先保存到本地 Obsidian，不自动推送 GitHub。

适用目录：

```text
D:\ObsidianVault
```

建议命令：

```text
/入库 这是一段经验或资料
/整理知识库 把最近内容整理成 Obsidian
/同步知识库 推送到 GitHub
```

### 第二阶段：岗位 SOP Skill 化

先做这些岗位 Skill：

- 项目经理 Skill：接需求、拆任务、排期、风险提醒。
- 品牌资料管理员 Skill：整理资料、维护 Obsidian、建立索引。
- 创意总监 Skill：定视觉方向、风格禁区、拍板方案。
- AI生图师 Skill：把 brief 转成稳定 prompt。
- 质检总监 Skill：检查真实性、平台适配、转化逻辑。
- 流程优化经理 Skill：复盘返工、压缩沟通成本、优化流程。

### 第三阶段：项目闭环 Skill

每个设计项目结束后，形成闭环：

```text
项目资料
-> 方案产出
-> 质检结果
-> 返工原因
-> 最终交付
-> 复盘结论
-> 新 Skill / 新 SOP
```

这能让公司知识库越来越值钱，而不是只保存聊天记录。

## 适合沉淀的公司级 Skill 清单

- [[30-资源/工具/Skill研究/知识入库 Skill]]
- [[30-资源/工具/Skill研究/Obsidian 笔记整理 Skill]]
- [[30-资源/工具/Skill研究/GitHub 知识库同步 Skill]]
- [[30-资源/工具/Skill研究/设计项目开项 Skill]]
- [[30-资源/工具/Skill研究/岗位任务分配 Skill]]
- [[30-资源/工具/Skill研究/电商详情页方案 Skill]]
- [[30-资源/工具/Skill研究/AI生图 Prompt Skill]]
- [[30-资源/工具/Skill研究/图片裁切 Skill]]
- [[30-资源/工具/Skill研究/图片质检 Skill]]
- [[30-资源/工具/Skill研究/项目复盘 Skill]]
- [[30-资源/工具/Skill研究/小红书图文生产 Skill]]

## 公开 Skill 资源清单

### 分类说明

这些资源不全是同一种“Skill”：

- 官方 Skill：由 Anthropic 等官方仓库维护，可直接作为 Skill 参考。
- 社区 Skill：个人或社区维护，需要先审查内容再使用。
- MCP / 插件：不是 Skill 本体，但能给 Agent 增加外部工具或上下文能力。
- 工作流框架：更像一套 Agent 使用规范、命令体系或虚拟团队流程。

### 资源索引

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

### 建议采用顺序

第一批先做知识库和 Skill 生产：

- Skill Creator
- Find Skills
- Agent Skill Creator
- Obsidian 入库相关自建 Skill

第二批再做岗位协同：

- Superpowers
- gstack
- Matt's Skills
- 自建项目经理 Skill
- 自建质检总监 Skill

第三批做内容分发：

- Xiaohongshu Image Skill
- Infographic Skill
- Cover Image Skill
- Markdown to HTML Skill
- Publish WeChat Skill
- Publish Weibo Skill

### 使用风险

- 社区 Skill 使用前必须看源码和 `SKILL.md`，不能直接信任。
- 发布类 Skill 可能需要账号权限或 token，不能把密钥写进知识库。
- MCP 工具会扩大 Agent 可访问范围，要明确权限边界。
- 内容生成类 Skill 必须符合品牌风格和平台规则。
- 外部 Skill 只能做参考，最终要沉淀成适合本公司岗位流程的内部 Skill。

## 风险提醒

- 不要把 Skill 变成一堆泛泛提示词，必须有输入、输出和验收标准。
- 不要自动推送 GitHub，先让用户确认。
- 不要把未确认内容写成事实。
- 不要在业务群里处理 bot 技术维护和 API 报错。
- 不要为降本牺牲质量，否则返工成本会更高。

## 下一步行动

- [ ] 把“知识入库 Skill”做成第一版固定流程。
- [ ] 把 Obsidian 目录规划和标签规范补齐。
- [ ] 把 AI设计公司岗位 SOP 拆成独立 Skill。
- [ ] 建立“项目复盘 -> SOP -> Skill”的循环。
- [ ] 用户确认后，再同步到 GitHub 知识库。

## 相关链接

- [[资源总览]]
- [[OpenClaw 企业微信多 Agent 教程分析]]
- [[飞书链接 Codex 成功经验]]
- [[飞书 Codex 项目复刻手册]]
