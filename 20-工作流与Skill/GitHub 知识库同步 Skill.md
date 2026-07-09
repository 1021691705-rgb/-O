# GitHub 知识库同步 Skill

## 一句话总结

把 Obsidian 知识库变更通过 Git 分支、提交、合并 main 的方式同步到公司 GitHub 仓库，并在提交说明中写清新增或调整了什么知识。

## 参考来源

- GitHub 官方文档：分支用于隔离工作，Pull Request / merge 用于把分支变更合并回主线。
- GitHub 官方文档：提交信息应说明变更内容。
- 本地仓库：`C:\Users\HTX-GaoJie\Documents\obsidian 2`
- 远端仓库：`https://github.com/1021691705-rgb/-O`
- 本机 GitHub 访问需要走本地 Clash 代理：`http://127.0.0.1:7890`

## 触发场景

- 任意知识库 Markdown 变更完成。
- 用户说“同步”“上传 GitHub”。
- 默认规则：知识库变更完成后自动同步，除非用户明确说不要上传。

## 输入

- 本次需要同步的文件列表。
- 新增知识主题。
- 是否有无关本地草稿需要避开。

## 输出

- 一个新建知识分支。
- 一个说明清楚的提交。
- 推送后的远端知识分支。
- 已合并并推送的 `main`。

## 标准流程

```text
检查 git status
-> 区分知识内容、Obsidian 本地状态和草稿文件
-> 只挑本次相关内容文件
-> 新建 knowledge/YYYY-MM-DD-topic 分支
-> git add 指定文件
-> git commit 写清新增知识
-> git push 分支
-> 切 main
-> merge --no-ff 知识分支
-> push main
-> 确认远端 refs/heads/main
```

## 提交边界

不要提交：

- `.obsidian/graph.json`
- `.obsidian/workspace.json`
- `.obsidian/workspace-mobile.json`
- `*.canvas`
- 附件草稿
- 其他未确认笔记
- 密钥、token、cookie

优先提交：

- 新增或修改的 `.md` 笔记。
- `.gitignore`、模板或总览页这类知识库规则文件。
- 必须共享的 Obsidian 配置，例如模板目录、每日笔记目录。

提交前检查：

```bash
git status --short --untracked-files=all
git diff --name-status
```

如果看到 `.obsidian/graph.json`、canvas 草稿或无关 Obsidian 状态文件，先不要加入提交。

如果 `.obsidian/graph.json` 已经被 Git 跟踪，先只从索引移除，保留本地文件：

```bash
git rm --cached -- .obsidian/graph.json
```

## 分支命名

```text
knowledge/2026-06-29-agent-skill-index
knowledge/2026-06-29-merge-agent-skill-notes
```

格式：

```text
knowledge/YYYY-MM-DD-topic
```

## 相关链接

- [[20-工作流与Skill/知识入库 Skill]]
- [[20-工作流与Skill/Obsidian 笔记整理 Skill]]
- [[20-工作流与Skill/知识库健康检查清单]]
- [[20-工作流与Skill/AI设计公司工作流地图]]
- AI生产力核弹：4组顶级Skill重构Agent工作流（待恢复）
