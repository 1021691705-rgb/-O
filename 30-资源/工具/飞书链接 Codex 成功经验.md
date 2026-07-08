# 飞书链接 Codex 成功经验

## 一句话总结

这个项目把飞书企业自建应用机器人接到本地 Codex：飞书用长连接把消息推给本机 Node.js 服务，本机服务解析消息、排队、调用 Codex CLI，再把文本或图片结果发回飞书。

## 经验来源

- 项目目录：`C:\Users\HTX\Documents\飞书`
- 项目名称：`feishu-codex-bot`
- 主要入口：`src/ws-server.ts`
- 启动脚本：`start-bot.cmd`
- 健康检查：`check-bot.cmd`
- 操作记忆：`OPERATION_MEMORY.md`
- 说明文档：`README.md`

注意：这篇笔记只记录结构、流程和经验，不保存真实密钥、token、cookie、API key 或完整隐私配置。

## 最终跑通的核心方案

最重要的选择是：**用飞书长连接模式，不再依赖公网 Webhook 和临时回调地址。**

```text
飞书消息
-> 飞书长连接事件 im.message.receive_v1
-> 本地 Node.js 服务 dist/ws-server.js
-> 解析消息、判断是否响应、去重、排队
-> Codex CLI / 生图接口 / AI设计公司模式
-> 飞书发送文本或图片回复
```

这个方案解决了旧 Webhook 模式里最麻烦的问题：本地服务重启后回调地址变化、Cloudflare 隧道不稳定、飞书后台需要反复改 URL。

## 项目结构

```text
C:\Users\HTX\Documents\飞书
  package.json
  README.md
  OPERATION_MEMORY.md
  .env.example
  start-bot.cmd
  stop-bot.cmd
  check-bot.cmd
  install-autostart.cmd
  uninstall-autostart.cmd
  scripts/
    start-bot.ps1
    stop-bot.ps1
    check-bot.ps1
    check-env.mjs
  src/
    ws-server.ts
    feishu.ts
    codex-runner.ts
    task-queue.ts
    store.ts
    workspaces.ts
    openai-image.ts
    codex-result-sender.ts
    local-images.ts
    design-company.ts
```

核心不是某一个文件，而是一条完整链路：

- `ws-server.ts`：总控入口，接收飞书事件，路由到 Codex、生图、AI设计公司。
- `feishu.ts`：封装飞书发消息、发图片、上传图片、下载图片、token 缓存、事件解析。
- `codex-runner.ts`：封装 `codex exec` 和 `codex exec resume`，维护线程、进度、超时、恢复失败处理。
- `task-queue.ts`：同一会话串行排队，避免并发改同一个工作区。
- `store.ts`：保存飞书会话和 Codex thread 的映射。
- `workspaces.ts`：支持不同飞书群绑定不同本地工作区。
- `openai-image.ts`：处理 `/生图`，包括尺寸解析、重试、尺寸校验、压缩。
- `codex-result-sender.ts`：把 Codex 回复里的本地图片路径自动上传到飞书。
- `design-company.ts`：AI设计公司模式，把一个机器人包装成多岗位协作。

## 飞书后台配置经验

飞书开放平台里需要做这些事：

```text
事件与回调
-> 事件配置
-> 使用长连接接收事件
-> 订阅 im.message.receive_v1
```

必须开通的权限：

```text
im:message.p2p_msg:readonly
im:message.group_at_msg:readonly
im:message:readonly
im:message:send_as_bot
```

如果要支持图片生成和图片回传，还要开：

```text
im:resource
im:resource:upload
```

关键经验：

- 改权限、改事件、改长连接模式后，必须创建并发布新版本。
- 如果机器人能主动发消息，但收不到飞书事件，优先检查“是否发布了新版应用”。
- 群聊里要确认机器人已经加入目标群，并且消息真正 @ 到机器人。
- 长连接是事件消费模式，不是广播模式；不要同时开多个实例消费同一个应用事件。

## 本地配置经验

`.env.example` 里把配置分成了几类：

```env
FEISHU_APP_ID=
FEISHU_APP_SECRET=
FEISHU_BOT_NAME=Codex
CODEX_COMMAND=codex.cmd
CODEX_WORKSPACE=C:\Users\HTX\Documents\飞书
CODEX_SANDBOX=workspace-write
CODEX_TIMEOUT_MS=1800000
QUEUE_MAX_PER_CONVERSATION=5
THREAD_STORE_PATH=.codex-threads.json
```

已经验证的注意点：

- `FEISHU_APP_ID` 和 `FEISHU_APP_SECRET` 是必填项。
- `FEISHU_VERIFICATION_TOKEN` 和 `FEISHU_ENCRYPT_KEY` 不是永远必填，但飞书后台启用后，本地必须同步填写。
- `CODEX_COMMAND` 在 Windows 上使用 `codex.cmd`。
- `CODEX_TIMEOUT_MS` 设置到 30 分钟，适合 Codex 长任务。
- `QUEUE_MAX_PER_CONVERSATION=5` 可以防止一个飞书会话堆太多任务。
- `FEISHU_MESSAGE_DEDUPE_TTL_MS` 用于消息去重，避免飞书重复投递导致重复执行。
- `FEISHU_MAX_UPLOAD_IMAGE_BYTES` 用于控制飞书上传图片大小。

### Windows 沙箱问题

项目记忆里记录过一个关键坑：

```text
windows sandbox: spawn setup refresh
os error 740
```

这通常是本地 `codex.cmd` 的 Windows sandbox helper 被系统拦截。当前项目曾用：

```env
CODEX_SANDBOX=danger-full-access
```

来绕过本地 CLI 的 Windows sandbox 初始化问题。

这个配置风险更高，因为它绕过了 Codex 的工作区沙箱。对应的安全补救是：

```env
FEISHU_ALLOWED_SENDER_IDS=
FEISHU_ALLOWED_CHAT_IDS=
```

也就是只允许可信用户或可信群调用机器人。

## 启动方式

日常最推荐双击：

```text
start-bot.cmd
```

停止：

```text
stop-bot.cmd
```

检查：

```text
check-bot.cmd
```

项目里的 `start-bot.cmd` 会先判断有没有安装计划任务：

- 如果有计划任务，就通过 Windows Task Scheduler 启动。
- 如果没有，就直接运行 `scripts/start-bot.ps1`。
- 启动日志写入 `.runtime/launcher.log`。
- 成功后窗口会自动关闭。

`scripts/start-bot.ps1` 做了完整启动流程：

```text
解析项目根目录
-> 创建 .runtime
-> 检查 .env
-> 如果 node_modules 不存在就 npm install
-> npm run check:env
-> npm run build
-> 停掉旧机器人进程
-> 清理旧日志
-> 后台启动 node dist/ws-server.js
-> 写入 LONG_CONNECTION_MODE: no callback URL required
```

一个很实用的设计：如果机器人已经在跑，且没有强制重启，会直接显示：

```text
ALREADY RUNNING
```

这样不会误杀正在处理中的 Codex 任务。

## 健康检查经验

`check-bot.cmd` / `npm run bot:check` 是这个项目的日常诊断入口。

它会检查：

- 机器人进程是否存在。
- 开机自启计划任务是否安装。
- 最近一次长连接 ready/connect 日志。
- 最近心跳。
- 最近收到的飞书事件。
- 最近 Codex 开始和完成记录。
- 最近失败日志。
- 当前是否有正在跑的 Codex 子任务。

排查顺序可以固定成：

```text
1. Bot process 是否 RUNNING
2. 最近心跳是否 status=connected
3. 发消息后有没有 received message event
4. 是否出现 Codex task started
5. 是否出现 Codex task finished
6. server.err.log 里有没有具体错误
7. Active Codex tasks 是否有长时间未结束任务
```

如果有活动 Codex 任务，不要随手重启。先看它是否真的卡死，否则会中断正在处理的回复。

## 消息处理链路

`ws-server.ts` 收到 `im.message.receive_v1` 后，会按这个顺序处理：

```text
提取文本和图片
-> 检查发送者/群聊白名单
-> 判断群聊是否 @ 机器人
-> 清理 @Codex / @机器人 等提及文本
-> 解析回复目标 chat_id 或 open_id
-> 解析工作区
-> 检查工作区是否存在
-> 消息去重
-> 判断是否 /生图
-> 判断是否 AI设计公司命令
-> 普通 Codex 任务排队执行
```

这里有几个值得保留的设计：

- 私聊默认直接响应。
- 群聊默认只响应真实 @、`@Codex`、`@codex`、`@机器人` 或配置的机器人名。
- 非文本消息不会乱处理。
- 图片消息会下载到临时目录，再作为 `codex exec -i` 的输入传给 Codex。
- 同一个会话串行排队，减少并发写文件风险。
- 先发“收到，我正在交给 Codex 处理”，再后台执行，体验更稳定。

## Codex 调用经验

`codex-runner.ts` 的核心做法：

```text
第一次对话：
codex exec --json --color never --sandbox ... -C 工作区 -o last-message.txt -

后续对话：
codex exec resume --json -o last-message.txt session_id -
```

关键点：

- prompt 从 stdin 传入，避免命令行参数太长。
- `-o last-message.txt` 用来稳定读取最后回复。
- `--json` 用来解析 Codex 进度事件。
- 每个飞书会话绑定一个 Codex session。
- session ID 存在 `.codex-threads.json`。
- 如果 resume 失败，并且错误像 session/thread 失效，会删除旧记录并新开会话。
- 超时会 kill 整个进程树，避免残留子进程。
- Windows 上 `.cmd` 命令要通过 `cmd.exe /d /s /c` 启动。

这个设计让飞书里的对话能保持上下文，同时又能在 Codex 线程失效时自动恢复。

## 进度反馈经验

长任务不能只让用户干等。项目里做了一个 `CodexProgressReporter`：

- 先发送确认消息。
- 一段时间后开始更新“Codex 正在处理”。
- 后续按间隔更新进度。
- 优先编辑原确认消息。
- 如果编辑失败，就回退为发送新消息。
- 完成时显示用时。
- 失败时显示用户可读的错误。

默认节奏来自 `.env.example`：

```env
CODEX_PROGRESS_FIRST_UPDATE_MS=15000
CODEX_PROGRESS_UPDATE_MS=45000
```

这很适合飞书：用户能知道任务没有丢，也不会被刷屏。

## 多群绑定多工作区

项目支持用一个机器人服务多个项目：

```env
FEISHU_CHAT_WORKSPACES=oc_xxx=C:\Users\HTX\Documents\项目A;oc_yyy=D:\Code\项目B
```

经验点：

- `oc_xxx` 是飞书群的 `chat_id`。
- 没配置的群和私聊使用默认 `CODEX_WORKSPACE`。
- 工作区路径必须是绝对路径。
- 启动后会检查工作区是否存在。
- Codex 线程按“会话 + 工作区”隔离。

线程 key 的思路是：

```text
chat:群ID|workspace:工作区绝对路径
```

这样同一个群切到不同工作区时，不会误复用旧 Codex 上下文。

## AI设计公司模式

项目不只是普通 Codex 转发，还内置了一个“AI设计公司”模式。

支持命令：

```text
/开项目
/分配任务
/开会
/出方案
/质检
```

它的做法不是创建多个飞书机器人，而是在同一个回复里让 Codex 按岗位标签协同：

```text
[项目经理]
[创意总监]
[平面设计师]
[电商详情页设计师]
[3D设计师]
[建模设计师]
[AI生图师]
[质检总监]
[流程优化经理]
```

经验点：

- 设计公司上下文和普通 Codex 上下文分开。
- 设计公司线程 key 后面追加 `:design-company`。
- 进入设计公司模式后，2 小时内追问会继续这个上下文。
- 发送 `/codex 你的问题` 可以临时切回普通 Codex。
- 只发 `/codex` 可以退出设计公司上下文。

这个模式适合电商设计、3D设计、详情页方案、图片质检和流程协作，不适合继续排查机器人技术问题。

## 生图能力经验

项目支持这些命令：

```text
/生图 prompt
生图 prompt
/image prompt
image prompt
```

并支持清晰度和比例：

```text
/生图 2K 1:1 头像
/生图 4K 16:9 横版壁纸
/生图 4K 9:16 手机壁纸
/生图 4K 4:3 场景图
/生图 2K 3:4 竖版海报
```

尺寸解析逻辑：

- `2K` 长边按 `2048` 计算。
- `4K` 长边按 `3840` 计算。
- 支持 `1:1`、`16:9`、`9:16`、`4:3`、`3:4`。
- 不写比例时默认 `1:1`。
- 不写尺寸时使用 `OPENAI_IMAGE_SIZE`。

项目里有一个重要策略：

- 如果请求尺寸超过 `2048`，实际请求接口时会先降到长边 `2048`。
- prompt 里会加入“严格输出目标尺寸和比例”的要求。
- 返回后如果比例一致但尺寸不够，会用 PowerShell 做高质量 resize 到目标尺寸。
- 如果返回比例不一致，拒绝裁剪、补边或拉伸，直接报错让用户重试或换接口。

这条经验很重要：**宁可失败，也不要偷偷把图片拉伸变形。**

## 已验证的生图经验

操作记忆里记录了：

```text
2026-06-28 生图 2K 方图请求成功
模型：gpt-image-2
请求尺寸：2048x2048
结果：2048x2048 PNG，约 4.56 MB
耗时：约 32.3s
```

还验证了：

- 支持 `2K`、`4K`。
- 支持 `1:1`、`16:9`、`9:16`、`4:3`、`3:4`。
- `npm run typecheck` 通过。
- `npm run build` 通过。

失败排查不要急着判断尺寸不支持。看到这些错误时，优先判断为网络或中转站超时：

```text
ConnectTimeoutError
UND_ERR_CONNECT_TIMEOUT
HeadersTimeoutError
UND_ERR_HEADERS_TIMEOUT
```

## 图片上传和压缩经验

飞书图片上传有大小限制，所以项目做了上传前处理：

- 如果图片小于 `FEISHU_MAX_UPLOAD_IMAGE_BYTES`，直接上传。
- 如果 PNG 太大，优先转成 JPEG。
- 逐步降低 JPEG 质量。
- 如果还太大，再按比例缩小。
- 上传后补发“图片信息”，包含：
  - 请求规格
  - 生成尺寸
  - 生成比例
  - 生成格式
  - 生成大小
  - 飞书上传尺寸
  - 飞书上传格式
  - 飞书上传大小
  - 是否压缩
  - 用时

这个设计让“图是不是被压缩过、是不是变形、尺寸有没有对上”都能追溯。

## Codex 结果里的本地图片回传

项目还有一个细节：如果 Codex 回复里包含本地图片路径，机器人会自动找图并上传到飞书。

默认允许的图片根目录：

- 当前 `CODEX_WORKSPACE`
- 当前用户的 `.codex/generated_images`

也可以通过：

```env
FEISHU_UPLOAD_IMAGE_ROOTS=
```

追加允许目录。

处理逻辑：

```text
扫描 Codex 回复里的本地图片路径
-> 确认路径在允许目录内
-> 移除回复里的本地路径文本
-> 上传图片到飞书
-> 发送图片消息
-> 如果部分失败，补发失败数量
```

这避免了飞书用户只看到一个本地路径，却打不开图。

## 安全经验

这个项目里真正需要持续坚持的安全规则：

- 不提交 `.env`。
- 不在笔记里记录真实 `FEISHU_APP_SECRET`。
- 不记录完整 API key。
- SDK debug 日志里可能有临时 `access_key` 和 `ticket`，日志输出前要脱敏。
- 用户可见错误里不要暴露 Bearer token、API key、内部请求体。
- 使用 `danger-full-access` 时必须限制可信用户或可信群。
- 停止脚本不能粗暴杀所有 Node，只能杀匹配本项目命令行的进程。

项目里已经做了这些处理：

- 飞书 SDK 日志脱敏。
- Codex 命令日志脱敏。
- 用户错误信息截断。
- Bearer / api key / secret / token 字段脱敏。
- `.env.example` 只放占位符。

## 成功后的日常使用方式

启动：

```bash
start-bot.cmd
```

检查：

```bash
check-bot.cmd
```

停止：

```bash
stop-bot.cmd
```

项目命令：

```bash
npm run check:env
npm run build
npm run bot:start
npm run bot:check
npm run bot:stop
```

飞书里可以这样用：

```text
普通问题：直接私聊 Codex
群聊问题：@Codex 你的问题
生图：/生图 2K 1:1 一个蓝色机械风 Codex 机器人头像
开项目：/开项目 做一套卧室衣柜 Amazon 主图和详情页
分配任务：/分配任务 这个项目需要主图、3D场景图、详情页逐屏方案
开会：/开会 讨论这套衣柜视觉方向
出方案：/出方案 先出首屏主图和详情页前三屏方案
质检：/质检 检查刚才这张图有没有产品结构变形
切回普通 Codex：/codex 你的问题
退出设计公司上下文：/codex
```

## 排查决策树

### 机器人完全没反应

```text
运行 check-bot.cmd
-> Bot process 是否 RUNNING
-> server.out.log 是否有 connected / status=connected
-> 飞书后台是否长连接模式
-> 应用是否发布新版
-> 机器人是否在目标聊天里
```

### 有心跳但收不到消息

```text
确认事件订阅 im.message.receive_v1
-> 确认权限已开
-> 确认改完后发布新版本
-> 群聊确认 @ 机器人
-> 确认没有另一个机器人实例抢走事件
```

### 收到事件但没有回复

```text
看 allowlist 是否限制了当前用户或群
-> 看 receive_id_type 是否正确
-> 看 im:message:send_as_bot 是否开通
-> 看 server.err.log 的 Feishu API 错误
```

### Codex 卡住或失败

```text
看 Active Codex tasks
-> 看 [codex] starting 命令
-> 看 Codex stderr
-> 如果 timeout，线程会被重置
-> 如果 resume/session 失效，会自动开新会话
```

### 生图失败

```text
看失败消息里的阶段
-> 生图阶段是否成功
-> 实际尺寸是多少
-> 上传前处理是否成功
-> 飞书上传是否成功
-> 飞书发送是否成功
-> 如果是 ConnectTimeout，优先查中转站连通性
```

## 这次项目最值得复用的经验

- 长连接比公网回调更适合本地桌面机器人。
- 一键启动脚本要包含环境检查、构建、旧进程处理和日志输出。
- 健康检查脚本比“凭感觉重启”可靠。
- 飞书机器人要有排队机制，否则很容易并发修改同一工作区。
- Codex 线程要持久化，但也要能在 resume 失效时自动重建。
- 长任务必须有进度反馈，否则用户会误以为机器人坏了。
- 生图要记录尺寸、格式、大小、压缩状态，方便复查。
- 对图片尺寸和比例，宁可明确失败，也不要暗中拉伸。
- 本地图片路径对飞书用户没用，应该自动上传成飞书图片。
- 使用高权限沙箱时，必须用飞书用户/群白名单兜底。

## 后续可以继续完善

- [ ] 把一次完整的“无回复”排查过程补成案例。
- [ ] 把一次完整的“生图失败”排查过程补成案例。
- [ ] 记录飞书后台每个页面的截图位置。
- [ ] 记录当前可用的群聊 `chat_id` 和对应工作区，但不要记录隐私群名。
- [ ] 给 AI设计公司模式单独写一篇使用手册。
- [ ] 给 `/生图` 单独写一篇尺寸、比例、压缩和排错手册。

## 相关链接

- [[30-资源/资源总览]]
- [[首页]]
