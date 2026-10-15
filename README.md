# Patent Agent Workflow Skill

面向中国专利申请前准备：从技术交底分析和创造性检索，形成权利要求、实验安排、说明书及文本一致性审查结果。当前仍在开发和实案验证中。

## 使用入口

Agent 从 [SKILL.md](SKILL.md) 按用户目标选择必要节点；节点顺序表示依赖关系，不要求每次走完。已授权的跨节点任务连续完成，具体方法和完成条件以入口及对应参考文件为准。

检索用于调整申请文本的保护边界。默认由用户取得全文，是基于人工下载效率和 HimmPat 额度的工作安排；扫描对比文件同样保留人工交接。完整边界见入口，抽取、检索和交接细节见 [检索工作流](references/inventive-search-workflow.md)。

## 规则维护位置

- 任务路由、修改范围及完成条件：[SKILL.md](SKILL.md)。
- 检索、HimmPat 业务验证与降级、网页操作入口、全文核验：[检索工作流](references/inventive-search-workflow.md)。
- 评述结构：[创造性分析模板](references/inventive-search-analysis-template.md)。
- 跨学科技术概念解释：[跨学科解释](references/cross-disciplinary-explanation.md)。
- 权要宽度与后备层级：[权要工作流](references/claim-drafting-workflow.md)；正文骨架：[权要模板](references/claim-drafting-template.md)。
- 实验分组与结果解释：[实验设计](references/experimental-design-workflow.md)。
- 说明书结构及上下游衔接：[说明书起草](references/specification-drafting-workflow.md)。
- 文本阶段、语体与证据表达：[专利文本语体](references/patent-text-style-and-evidence.md)。
- 术语、引用链、跨文件关系及复核范围：[一致性审计](references/terminology-and-consistency-workflow.md)。
- 指南原件路径、版本、章节与抽取方法：[指南读取规则](references/examination-guidelines.md)。
- 一案一库及 Markdown 历史：[案件版本管理](references/case-markdown-git.md)。

同一规则在上述负责文件维护，其他位置引用入口，避免复制后各自变化。案件材料和底稿保存在案件目录，技能维护不改真实案件正文。

## 本地维护

项目目录是技能源码仓库。本机 `C:/Users/Administrator/.codex/skills/patent-agent-workflow-skill` 当前为指向本目录的 Junction；修改这里会直接改变启用的技能文件，无需复制另一份。修改前保留现有未提交内容，按本轮任务控制差异。

修改技能后，在本目录运行结构校验：

```powershell
python -X utf8 "C:\Users\Administrator\.codex\skills\.system\skill-creator\scripts\quick_validate.py" "."
```

校验只检查技能结构，不证明专业判断正确。改变路由或边界时，用少量合成任务或隔离副本验证实际行为：只审查不改、局部编号修改、全文齐备的跨节点任务、缺全文交接、无实验数据的起草。观察是否提前停止、过度读取、越界修改或套用预置结论；只重测受修改影响的场景，不将这组验证变成每次办案的必跑步骤。实案检出能力仍需约定范围的试点。

## License

尚未选择许可证。添加 `LICENSE` 文件前，不默认授予再分发许可。
