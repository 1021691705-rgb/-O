# photoshop-mcp

## 一句话总结

`alisaitteke/photoshop-mcp` 是一个面向 Photoshop 的 MCP 工具仓库，目标是让 Codex、Claude、Cursor 等 AI 助手通过 MCP 连接 Photoshop 并执行操作。

## 来源

- GitHub：https://github.com/alisaitteke/photoshop-mcp
- 入库日期：2026-07-09
- 用户补充：这是 PS 的 MCP，专门让 Codex 连接 PS 进行操作。
- 状态：已记录为候选工具；安装命令、版本兼容和权限范围需要正式使用前复核仓库 README。

## 适合解决什么问题

- 让 Codex 通过 MCP 获取 Photoshop 当前文档、图层、选择区和预览状态。
- 让 AI 助手执行 Photoshop 自动化操作，例如裁切、导出、抠图、蒙版、调整图层或批处理。
- 将原本依赖人工点击 Photoshop UI 的步骤，沉淀为更稳定的工具调用流程。
- 为 [[30-视觉与内容生产/Photoshop 裁切导出 Skill]] 这类 Photoshop 工作流提供更直接的自动化底层能力。

## 为什么值得关注

- 当前知识库里已经有 Photoshop 裁切、图片裁切、图片质检等工作流。
- 如果 MCP 能稳定连接 Photoshop，后续可以减少手动 UI 操作，提高批量裁图、导出和图层处理效率。
- MCP 接入后，Codex 可以先读取 Photoshop 状态，再决定下一步动作，比纯键鼠自动化更可控。

## 使用前检查

1. 确认仓库是否仍在维护，README 是否有最新安装说明。
2. 确认支持的 Photoshop 版本、系统版本和脚本权限。
3. 确认 MCP server 需要访问哪些本地目录、网络接口和 Photoshop 自动化权限。
4. 先用非客户素材测试打开文档、读取图层、获取预览、导出文件等基础动作。
5. 验证失败时是否有清晰错误信息，避免误改原始 PSD 或覆盖交付文件。

## 风险和注意事项

- 这是社区仓库，不能默认等同于 Adobe 官方支持。
- Photoshop 自动化涉及真实文件操作，正式使用前必须确认输出目录和覆盖规则。
- 涉及客户素材时，要避免把私密图片、PSD、字体或品牌文件上传到外部服务。
- 如果 MCP 与现有 Photoshop UI 自动化、Flue 或其他脚本工具并用，需要明确谁负责打开文件、谁负责导出、谁负责清理临时文件。

## 后续行动

- [ ] 阅读并复核仓库 README 的安装步骤。
- [ ] 在本机测试 MCP 是否能连接 Photoshop。
- [ ] 记录可用工具清单：状态读取、预览、图层、选择区、裁切、导出、批处理。
- [ ] 如果稳定可用，升级 [[30-视觉与内容生产/Photoshop 裁切导出 Skill]] 的底层执行方式。
- [ ] 补一篇“Codex 连接 Photoshop MCP 操作手册”。

## 相关链接

- [[30-视觉与内容生产/Photoshop 裁切导出 Skill]]
- [[30-视觉与内容生产/图片裁切 Skill]]
- 图片质检 Skill（待恢复）
- [[20-工作流与Skill/AI设计公司工作流地图]]
- [[40-工具与系统/资源总览]]
