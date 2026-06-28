# 飞书 Codex 项目复刻手册

## 目标

在另一台电脑上复刻飞书连接本地 Codex 的机器人，让飞书消息可以进入本地 Codex，并把 Codex 的文本或图片结果发回飞书。

## 原项目信息

- 原项目目录：`C:\Users\HTX\Documents\飞书`
- 项目名称：`feishu-codex-bot`
- 核心入口：`src/ws-server.ts`
- 启动方式：`start-bot.cmd`
- 健康检查：`check-bot.cmd`
- 经验来源：[[飞书链接 Codex 成功经验]]

## 复刻原则

- 代码可以复制，`.env` 要重新配置。
- 不要把真实密钥、token、cookie、API key 写进 Obsidian。
- 新旧电脑不要同时运行同一个飞书应用，否则长连接事件可能被其中一台消费。
- 先验证普通文本链路，再验证 `/生图`。
- 每次踩坑后，把现象、根因、解决方式补回这篇手册。

## 新电脑准备清单

- [ ] 安装 Node.js。
- [ ] 安装 Git。
- [ ] 安装 Codex CLI。
- [ ] 登录 Codex。
- [ ] 安装飞书客户端。
- [ ] 复制项目代码到新电脑。
- [ ] 准备飞书企业自建应用的配置。

## 建议项目路径

新电脑也建议放到文档目录：

```text
C:\Users\你的用户名\Documents\飞书
```

复制项目时可以不带这些目录：

```text
node_modules
dist
.runtime
```

这些目录可以在新电脑重新安装、构建和生成。

## 需要重新配置的内容

在新电脑项目目录里复制：

```bash
copy .env.example .env
```

然后填写 `.env`。至少检查这些项：

```env
FEISHU_APP_ID=
FEISHU_APP_SECRET=
FEISHU_BOT_NAME=Codex
CODEX_COMMAND=codex.cmd
CODEX_WORKSPACE=C:\Users\你的用户名\Documents\飞书
CODEX_SANDBOX=danger-full-access
CODEX_TIMEOUT_MS=1800000
QUEUE_MAX_PER_CONVERSATION=5
THREAD_STORE_PATH=.codex-threads.json
```

如果飞书后台启用了事件安全，还要同步填写：

```env
FEISHU_VERIFICATION_TOKEN=
FEISHU_ENCRYPT_KEY=
```

如果要复刻生图能力，再填写：

```env
OPENAI_IMAGE_API_KEY=
OPENAI_IMAGE_BASE_URL=
OPENAI_IMAGE_MODEL=
OPENAI_IMAGE_SIZE=2048x2048
OPENAI_IMAGE_QUALITY=medium
OPENAI_IMAGE_FORMAT=png
```

## 飞书后台检查清单

进入飞书开放平台的企业自建应用，确认：

- [ ] 机器人能力已开启。
- [ ] 机器人已加入目标用户或群聊。
- [ ] 事件订阅方式是长连接。
- [ ] 已订阅 `im.message.receive_v1`。
- [ ] 已开通私聊消息读取权限。
- [ ] 已开通群聊 @ 消息读取权限。
- [ ] 已开通消息读取权限。
- [ ] 已开通机器人发消息权限。
- [ ] 如果用 `/生图`，已开通图片资源上传权限。
- [ ] 改过权限、事件或长连接模式后，已经发布新版应用。

关键权限：

```text
im:message.p2p_msg:readonly
im:message.group_at_msg:readonly
im:message:readonly
im:message:send_as_bot
im:resource
im:resource:upload
```

## 复刻步骤

1. 在旧电脑运行 `stop-bot.cmd`，先停掉旧机器人。
2. 把项目复制到新电脑。
3. 在新电脑复制 `.env.example` 为 `.env`。
4. 修改 `.env` 里的飞书配置、Codex 配置和新电脑路径。
5. 打开命令行进入项目目录。
6. 运行 `npm install`。
7. 运行 `npm run check:env`。
8. 运行 `npm run build`。
9. 双击或运行 `start-bot.cmd`。
10. 运行 `check-bot.cmd`。
11. 在飞书里私聊机器人发送普通文本。
12. 普通文本成功后，再测试群聊 @ 机器人。
13. 如果配置了生图，再测试 `/生图 2K 1:1 一个简单图标`。

## 验证标准

`check-bot.cmd` 中应该看到：

```text
Bot process: RUNNING
Long-connection bot is ready
status=connected
received message event
Codex task started
Codex task finished
```

飞书侧应该验证：

- [ ] 私聊机器人能收到文本回复。
- [ ] 群聊 @ 机器人能收到文本回复。
- [ ] Codex 能保持上下文。
- [ ] `/生图` 能返回图片。
- [ ] 机器人没有重复回复同一条消息。

## 常见问题排查

### 新电脑收不到飞书消息

优先检查：

- 旧电脑是否还在运行同一个机器人。
- 飞书后台是否选择长连接模式。
- 是否订阅了 `im.message.receive_v1`。
- 权限变更后是否发布了新版应用。
- 机器人是否加入目标聊天。
- 群聊里是否真正 @ 到机器人。

### 机器人运行了但 Codex 没反应

优先检查：

- `codex --version` 是否可用。
- `.env` 里的 `CODEX_COMMAND` 是否是 `codex.cmd`。
- `CODEX_WORKSPACE` 是否存在。
- `npm run build` 是否通过。
- `server.err.log` 是否有 Codex 错误。

### 出现 Windows 沙箱错误

现象可能类似：

```text
windows sandbox: spawn setup refresh
os error 740
```

解决方向：

```env
CODEX_SANDBOX=danger-full-access
```

但这个模式权限更高，建议同时配置飞书白名单：

```env
FEISHU_ALLOWED_SENDER_IDS=
FEISHU_ALLOWED_CHAT_IDS=
```

### 生图失败

优先看失败信息里的阶段：

- 生图阶段
- 实际尺寸
- 上传前处理
- 飞书上传
- 飞书发送
- 错误原因

如果看到这些错误，优先检查中转站和网络，不要直接判断为尺寸不支持：

```text
ConnectTimeoutError
UND_ERR_CONNECT_TIMEOUT
HeadersTimeoutError
UND_ERR_HEADERS_TIMEOUT
```

## 复刻记录

### 第一次复刻

- 日期：
- 新电脑：
- 项目路径：
- 是否成功：
- 遇到的问题：
- 根因：
- 最终解决方式：
- 下次注意：

## 相关链接

- [[飞书链接 Codex 成功经验]]
- [[资源总览]]
- [[首页]]
