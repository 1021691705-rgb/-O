# OpenClaw 企业微信多 Agent 教程分析

## 一句话总结

这个教程的核心价值是：把企业微信智能机器人接入 OpenClaw，并通过多 Agent 配置，让不同企业微信应用、用户或群组路由到不同的 Agent，从而实现上下文、记忆、工作区和职责隔离。

## 来源

- 腾讯文档：`https://docs.qq.com/mind/DY0RNdmpTemRSbHJV`
- 相关腾讯云开发者文章：`https://cloud.tencent.com/developer/article/2636375`
- 腾讯云官方企业微信接入文档：`https://cloud.tencent.com/document/product/1689/128999`
- 腾讯云多 Agent 配置参考：`https://www.tencentcloud.com/techpedia/142821?lang=zh`

说明：腾讯文档页面当前在浏览器中显示“不支持您的浏览器”，正文没有完整读取到。当前笔记基于标题、可检索到的同主题文章、腾讯云官方文档和 OpenClaw 多 Agent 配置说明整理。后续如果拿到腾讯文档截图或导出正文，需要继续补全。

## 教程主题判断

标题是：

```text
基于 OpenClaw 的企业微信多 Agent
辅助安装包流程以及功能介绍
```

可以拆成三件事：

- 企业微信接入 OpenClaw。
- OpenClaw 多 Agent 配置。
- 辅助安装包的使用流程和功能说明。

它不是单纯的“装一个聊天机器人”，而是在做一个企业微信里的多智能体系统。

## 核心目标

让企业微信中的不同入口，对应 OpenClaw 中不同的 Agent。

典型目标：

- 不同用户拥有自己的专属机器人。
- 不同群聊绑定不同业务 Agent。
- 不同企业微信应用对应不同 Agent。
- 每个 Agent 拥有独立工作区、记忆、会话、技能和模型。
- 降低单 Agent 承担太多任务造成的记忆污染和 token 消耗。

## 推荐理解模型

```text
企业微信用户或群聊
-> 企业微信智能机器人 API
-> OpenClaw 企业微信通道
-> bindings 路由规则
-> 指定 Agent
-> 独立 workspace / memory / session / skills / model
-> 回复企业微信
```

这和 [[40-工具与系统/飞书链接 Codex 成功经验]] 里的思路很像：

```text
IM 消息
-> 长连接或回调
-> 本地/云端服务
-> 路由
-> Agent 或 Codex
-> 回复 IM
```

区别是：

- 飞书 Codex 项目更像“一个机器人接本地 Codex”。
- OpenClaw 企业微信项目更像“一个 IM 通道接多个 Agent”。

## 企业微信接入要点

企业微信侧重点是创建智能机器人，并选择 API 模式。

接入方式通常有两种：

- 长连接方式。
- URL 回调方式。

更推荐长连接，因为它减少公网 URL、备案域名、回调地址变更等麻烦。

企业微信后台大致路径：

```text
企业微信管理后台
-> 安全与管理
-> 管理工具
-> 智能机器人
-> 创建机器人
-> 手动创建
-> API 模式创建
-> 选择长连接
-> 获取 Bot ID 和 Secret
```

## OpenClaw 通道配置要点

OpenClaw 侧需要安装企业微信通道插件，并添加通道。

常见命令：

```bash
openclaw plugins install @wecom/wecom-openclaw-plugin
openclaw gateway restart
openclaw channels add
```

通道配置选择：

```text
选择通道：WeCom 企业微信
接入模式：Bot 模式
接入方式：WebSocket 长连接模式
填写：Bot ID 和 Secret
私聊 DM 访问策略：配对模式
```

## 多 Agent 的关键机制

OpenClaw 多 Agent 的核心不是多开几个机器人，而是配置多个相互隔离的 Agent。

每个 Agent 可以拥有：

- 独立 workspace。
- 独立记忆。
- 独立会话。
- 独立技能。
- 独立模型。
- 独立业务职责。

核心配置点：

- `agents.list`：定义有哪些 Agent。
- `bindings`：决定某个渠道、用户、群或应用路由到哪个 Agent。
- `workspace`：隔离数据和记忆。
- `model`：可给不同 Agent 配不同模型。

## bindings 是关键

多 Agent 是否成功，取决于消息能不能被正确路由。

可以理解成：

```text
收到企业微信消息
-> 匹配 channel / peer / app / user / group
-> 找到对应 binding
-> 交给 binding 指定的 agentId
```

风险点：

- `bindings` 配错，消息会进默认 Agent。
- `agentId` 写错，Agent 不生效。
- 匹配条件不够具体，可能被更宽泛规则覆盖。
- 使用 `openclaw config set --json bindings` 可能整体覆盖原有 bindings。

重要经验：改 bindings 前一定备份配置。

```bash
cp ~/.openclaw/openclaw.json ~/.openclaw/openclaw.json.backup.$(date +%Y%m%d_%H%M%S)
openclaw config get bindings > /tmp/bindings-backup.json
```

## 多 Agent 配置示例思路

创建一个文档 Agent：

```bash
openclaw agents add --workspace /root/.openclaw/workspace-wecom-docs wecom-docs
```

查看 Agent：

```bash
openclaw agents list
```

把企业微信入口绑定到这个 Agent：

```bash
openclaw config set --json bindings '[
  {
    "agentId": "wecom-docs",
    "match": {
      "channel": "wecom",
      "peer": {
        "kind": "group",
        "id": "企业微信群或会话ID"
      }
    }
  }
]'
```

注意：上面只是结构示例。实际字段名要以当前 OpenClaw 企业微信插件和教程原文为准。

## 辅助安装包应该重点记录什么

如果教程里提到“辅助安装包”，知识库里不要只记录“下载并安装”，而要记录这些信息：

- 安装包解决什么问题。
- 安装包会改哪些配置。
- 是否会安装企业微信通道插件。
- 是否会生成或修改 `openclaw.json`。
- 是否会重启 gateway。
- 是否会写入 Agent / bindings / workspace。
- 是否支持回滚。
- 是否会保存 Bot ID、Secret 等敏感信息。
- 出错日志在哪里。
- 如何验证安装成功。

辅助安装包最应该沉淀成一张“安装前后变化表”：

```text
安装前：
- OpenClaw 是否已部署
- 企业微信机器人是否已创建
- 是否有 Bot ID 和 Secret
- 是否已有 bindings

安装后：
- 新增了哪些 Agent
- 新增了哪些 workspace
- 新增了哪些 bindings
- 新增了哪些 channel
- gateway 是否重启
- 日志是否正常
```

## 复刻流程

### 1. 准备 OpenClaw

- [ ] 有可运行的 OpenClaw 实例。
- [ ] 可以执行 `openclaw -v`。
- [ ] 可以进入服务器命令行。
- [ ] 已知道配置文件位置，例如 `~/.openclaw/openclaw.json`。

### 2. 创建企业微信机器人

- [ ] 企业微信有管理员权限。
- [ ] 进入智能机器人管理。
- [ ] 创建 API 模式机器人。
- [ ] 优先选择长连接。
- [ ] 获取 Bot ID。
- [ ] 获取 Secret。
- [ ] 设置可见范围。

### 3. 安装企业微信通道

- [ ] 安装 WeCom 插件。
- [ ] 重启 gateway。
- [ ] 添加通道。
- [ ] 选择 Bot 模式。
- [ ] 选择 WebSocket 长连接。
- [ ] 填写 Bot ID 和 Secret。
- [ ] 完成通道配置。

### 4. 配置多个 Agent

- [ ] 明确每个 Agent 的职责。
- [ ] 为每个 Agent 创建独立 workspace。
- [ ] 给 Agent 命名。
- [ ] 如有需要，为 Agent 指定模型。
- [ ] 验证 `openclaw agents list`。

### 5. 配置路由 bindings

- [ ] 备份 `openclaw.json`。
- [ ] 导出现有 bindings。
- [ ] 在原有 bindings 基础上追加新规则。
- [ ] 写回配置。
- [ ] 重启 gateway。
- [ ] 检查 gateway 状态。

### 6. 验证隔离

- [ ] 在企业微信入口 A 询问当前 workspace。
- [ ] 在企业微信入口 B 询问当前 workspace。
- [ ] 让 A 记住一条测试信息。
- [ ] 在 B 询问同一信息。
- [ ] 如果 B 不知道，说明记忆隔离成功。

## 验证标准

成功不只是“机器人能回复”，还要验证隔离效果。

最低验证：

- 企业微信能收到机器人回复。
- OpenClaw gateway 正常运行。
- 消息进入正确 Agent。
- 不同 Agent 的 workspace 不同。
- 不同 Agent 的记忆互不污染。
- 不同 Agent 的会话互不串线。

推荐测试问题：

```text
你当前所在的工作空间路径是什么？
请记住：这个入口的代号是 A。
你还记得这个入口的代号吗？
```

在另一个 Agent 入口里问：

```text
你知道入口 A 的代号吗？
```

如果不知道，说明隔离是有效的。

## 常见坑

### 1. 把普通 Webhook 机器人当 API 长连接机器人

企业微信里机器人类型容易选错。OpenClaw 企业微信 Bot 模式通常需要 API 模式机器人。

### 2. Secret 和 Token 混用

长连接模式一般关注 Bot ID 和 Secret；URL 回调模式会涉及 Token 和 Encoding-AESKey。两套配置不要混。

### 3. 覆盖了原有 bindings

`openclaw config set --json bindings` 很可能是整体替换，不是追加。改前必须备份。

### 4. Agent 创建了，但消息没有路由过去

只创建 Agent 不够，还要配置 binding。否则消息仍然可能进默认 Agent。

### 5. 群或应用 ID 填错

路由匹配依赖 ID。ID 错一位，Agent 就不会生效。

### 6. Gateway 没重启

改配置后不重启 gateway，新配置可能不会生效。

### 7. 多 Agent 没有验证记忆隔离

只测回复是不够的，要测试 workspace 和 memory 是否真的隔离。

## 和飞书 Codex 项目的关系

这篇教程可以和你的飞书项目互相参考。

可复用经验：

- 优先长连接，减少公网回调 URL 的麻烦。
- 先跑通单 Agent，再扩展多 Agent。
- 所有密钥只放配置文件，不进知识库。
- 改路由前备份配置。
- 用 allowlist / 可见范围控制访问。
- 通过“当前 workspace 是什么”验证路由。
- 通过“记住一条测试信息”验证记忆隔离。

不同点：

- 飞书 Codex 项目自己写 Node.js 服务，直接调用 Codex CLI。
- OpenClaw 企业微信方案依赖 OpenClaw gateway、channel、Agent、bindings。
- 飞书项目的多工作区是自己实现的 `FEISHU_CHAT_WORKSPACES`。
- OpenClaw 的多 Agent 是平台级配置，隔离能力更完整。

## 适合沉淀成哪些后续笔记

- OpenClaw 企业微信多 Agent 复刻手册
- OpenClaw bindings 配置经验
- [[40-工具与系统/企业微信智能机器人 API 模式]]
- OpenClaw 多 Agent 隔离验证方法
- IM 机器人长连接与 Webhook 对比

## 后续补充清单

- [ ] 补腾讯文档原文截图或导出内容。
- [ ] 补辅助安装包的实际下载地址。
- [ ] 补辅助安装包安装前后文件变化。
- [ ] 补企业微信后台真实操作截图。
- [ ] 补一次实际复刻记录。
- [ ] 补 `openclaw.json` 中真实但脱敏的多 Agent 配置示例。

## 相关链接

- [[40-工具与系统/飞书链接 Codex 成功经验]]
- [[40-工具与系统/飞书 Codex 项目复刻手册]]
- [[40-工具与系统/资源总览]]
- [[00-入口与收集/首页]]
