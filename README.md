# Patent Agent Workflow Skill

面向乙方专利代理师的申请前工作流 Skill。它将甲方尚未提交的技术交底书，转化为可用于专利申请准备的技术逻辑，并用创造性检索校验保护边界。

当前版本聚焦于：

- 理解交底书并拆分技术特征；
- 提取检索关键词和组合检索式；
- 发现、筛选和交接候选对比文件；
- 按最接近现有技术、区别特征、技术效果、实际技术问题和结合启示分析创造性；
- 将检索结论反馈给权利要求；
- 起草合理做宽的独立权利要求，并设置分层具体的从属后备方案；
- 按固定骨架起草说明书，给技术人员的说明写进具体实施方式；
- 按评述缺的对照和权要区间排实验设计，给甲方看，不写入申请文本。

项目状态：开发中。当前已固化创造性检索、权利要求起草、实验设计、说明书起草，以及案件 Markdown 的 git 版本管理。给甲方看的权要中间稿按合理做宽写。说明书按发明名称、技术领域、背景技术、发明内容、具体实施方式写；给技术人员的说明写进具体实施方式。除实验设计外不用表格。

## 工作边界

本项目默认处理申请前案件。用户负责下载需要深入比对的专利或其他资料，Agent 负责提出检索式、候选文件、优先级和分析框架；文件放入案件目录后，再进行全文核验。

本项目目前不自动下载专利，也不覆盖审查意见答复、专利通俗解读、CAD/线稿、Word 排版、政策监测或技能进化。

## 怎么用

Agent 入口是 `SKILL.md`：按用户意图和案件已有文件进入一个节点，不要按下面模块从头跑完。

```text
交底理解 → 检索交接 → 全文核验 → 创造性评述 → 权要 → 实验设计 → 说明书
```

各节点的做法和硬约束以 `SKILL.md` 及对应 `references/` 为准。交底、评述、权要、实验设计、说明书是一条链：评述第三节给说明书背景末句，评述第二节给有益效果，评述区别给权要分层，评述缺的对照给实验设计；后一文件不得另起口径。检索显示创造性不足时，先区分补检索、补实验、收窄权要还是改方案，不要默认重写全部文件。

## 人机卡点

检索默认在两处停下来交给用户。备用方案更慢，专利还会消耗 HimmPat API 额度；未经用户要求不要走备用。卡点政策以 `SKILL.md` 为准，此处只说明为什么先停。

1. **需要专利、论文或其他全文**：列出待下载清单后停。专利虽可用 HimmPat MCP 取全文，耗额度；论文没有下载 API，网页还要过爬虫。
2. **AnyDoc 转不出可用 Markdown**（多为图片型 PDF）：请用户从 HimmPat、CNIPA 等拷官方文本。截图逐页读、或用 HimmPat 看专利细节都可以，但更慢，后者耗额度。不装 OCR。

## 目录

```text
SKILL.md
references/
  inventive-search-workflow.md
  inventive-search-analysis-template.md
  claim-drafting-workflow.md
  claim-drafting-template.md
  specification-drafting-workflow.md
  experimental-design-workflow.md
  case-markdown-git.md
案件目录中的工作底稿（按需建立）/
  00_案件状态.md
  01_交底事实与术语.md
  02_检索要素与检索日志.md
  03_特征-文件-效果-证据.md
  04_保护范围决策.md
  05_说明书支撑与实施例计划.md
  06_文本逻辑审计.md
```

- `SKILL.md`：Agent 入口。只负责选节点和硬约束；细节在 `references/`。
- `references/inventive-search-workflow.md`：创造性检索和用户交接的详细工作流。
- `references/inventive-search-analysis-template.md`：案件创造性评述正文骨架（一至五节，不用表格；区别和辅助文件按实际数量写，不一对一配对）。
- `references/claim-drafting-workflow.md`：从检索结论和交底事实起草权利要求的规则，重点是独立权利要求合理做宽和从属权利要求分层后备。
- `references/claim-drafting-template.md`：案件目录中《权利要求书.md》的起草模板，含内部特征映射区和可整理为正文的权利要求区。
- `references/specification-drafting-workflow.md`：说明书骨架、背景末句用评述技术问题、发明内容支撑权要并收于有益效果、具体实施方式用中心配方加其余组变量；组编号跟锁定的实验设计；不用表格，不另建填写接口。
- `references/experimental-design-workflow.md`：给甲方的实验安排。一张表、成对对照句；中心组为实施例1。不写入申请文本。
- `references/case-markdown-git.md`：在案件目录 `git init`，只跟踪 Markdown；正文用稳定文件名，不另存暂行稿。`实验设计.md` 入库，xlsx 实验表不入库。
- 案件目录中的工作底稿：连接交底书、检索分析和专利文本；其中 `V1、V2` 只用于区分实质变化的技术方案，当前版本优先，旧版本不与当前版本混写。

## 工具说明

### HimmPat

每次检索先验证 HimmPat 业务 MCP：优先调用 `product_patent_discovery`，确认 `initialize`、`tools/list` 和一次实际检索调用均成功。验证成功后优先使用 HimmPat MCP；只有业务 MCP 未注入、连接/业务调用失败或认证失败时，才降级到 Exa。网页会话与 API Bearer 密钥是不同路径；In-App Browser 仅用于页面级查看或用户明确要求的网页下载，不作为 MCP 可用性判断。Exa 也不可用时，可使用 Codex 内置上网/浏览工具；仍无法检索则输出可复制的分组检索式和候选文件清单，不把其他来源冒充为 HimmPat 结果。

API 密钥轮换时，在本机 Codex 配置的相关 `[mcp_servers.product_*]` 段落中，将 `headers.Authorization` 的 `Bearer <旧密钥>` 替换为新密钥，并重新加载 Codex MCP。多个端点共用同一密钥时一并替换；不要把真实密钥写入仓库、README、提交记录或对话。网页会话过期时，直接在 HimmPat In-App Browser 中重新登录，不能用更换 API 密钥替代网页登录。

### Exa

Exa 可用于发现专利、论文、学位论文、会议资料、预印本和技术报告等候选对比资料。摘要、高亮和提取文本只用于初筛；高价值结果即使无法直接打开或存在付费墙，也应输出标题、作者/申请人、公开编号或 DOI、原始 URL、命中特征、价值理由和待核实内容，交给用户取得全文。Exa 不可用时，可改用 Codex 内置上网/浏览工具，并注明实际渠道。

### 文档读取

含可提取文本的 DOCX、PDF、PPTX、XLSX 优先使用 Firecrawl AnyDoc 转为 Markdown；AnyDoc 不可用或转换失败时，改用 Python/OOXML 等方式读取。扫描件或图片型 PDF：停，请用户拷官方文本，见上文「人机卡点」。

## 关键词组合式

每轮检索先整理 2～8 个有意义的语义单位，向用户提供可复制的组合式：

```text
窄检索：
(对象词1 OR 对象词2) AND (核心组分/结构词1 OR 核心组分/结构词2) AND (用途/场景词1 OR 用途/场景词2)

关系/工艺检索：
(核心组分/结构词) AND (关系词1 OR 关系词2) AND (工艺步骤/条件词1 OR 工艺步骤/条件词2)

效果检索：
(对象词 OR 上位对象词) AND (核心组分/结构词) AND (性能目标1 OR 性能目标2)

机理辅助检索：
(核心组分/结构词) AND (迁移/键合/交联等机理词) AND (用途/场景词)
```

`/TAC` 仅在目标平台支持时使用；英文完整词组应使用双引号。先做窄检索，再逐步删除一个限定组或改用上位概念扩大，并记录每组检索的渠道、日期和用途。

## 本地开发

本项目是普通 Git 仓库。修改 `SKILL.md` 或 `references/` 后，使用 Codex 的 Skill 校验脚本检查：

```powershell
$env:PYTHONUTF8 = "1"
python "C:\Users\Administrator\.codex\skills\.system\skill-creator\scripts\quick_validate.py" "."
```

Skill 通过实际案件验证后再扩展专利文本工作流或其他模块，避免将未确认的规则过早固化。

## License

尚未选择许可证。添加 `LICENSE` 文件前，不默认授予再分发许可。
