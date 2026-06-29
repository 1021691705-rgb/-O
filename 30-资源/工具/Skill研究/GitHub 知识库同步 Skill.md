# GitHub 知识库同步 Skill

## 一句话总结

把 Obsidian 知识库变更通过 Git 分支、提交、合并 main 的方式同步到公司 GitHub 仓库，并在提交说明中写清新增或调整了什么知识。

## 参考来源

- GitHub 官方文档：分支用于隔离工作，Pull Request / merge 用于把分支变更合并回主线。
- GitHub 官方文档：提交信息应说明变更内容。
- 本地仓库：`D:\ObsidianVault`
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
-> 只挑本次相关文件
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
- 未命名 canvas
- 附件草稿
- 其他未确认笔记
- 密钥、token、cookie

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

- [[30-资源/工具/Skill研究/知识入库 Skill]]
- [[30-资源/工具/Skill研究/Obsidian 笔记整理 Skill]]
- [[AI生产力核弹：4组顶级Skill重构Agent工作流]]
