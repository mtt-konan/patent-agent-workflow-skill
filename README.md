# Patent Agent Workflow Skill

面向乙方专利代理师的申请前工作流 Skill。它将甲方尚未提交的技术交底书，转化为可用于专利申请准备的技术逻辑，并用创造性检索校验保护边界。

当前版本聚焦于：

- 理解交底书并拆分技术特征；
- 提取检索关键词和组合检索式；
- 发现、筛选和交接候选对比文件；
- 按最接近现有技术、区别特征、技术效果、实际技术问题和结合启示分析创造性；
- 将检索结论反馈给权利要求、说明书和实验设计。

项目状态：开发中。当前已固化创造性检索工作流，专利文本完整写作规则将在实际案件验证后补充。

## 工作边界

本项目默认处理申请前案件。用户负责下载需要深入比对的专利或其他资料，Agent 负责提出检索式、候选文件、优先级和分析框架；文件放入案件目录后，再进行全文核验。

本项目目前不自动下载专利，也不覆盖审查意见答复、专利通俗解读、CAD/线稿、Word 排版、政策监测或技能进化。

## 核心流程

```text
交底书理解
  → 技术特征和检索关键词提取
  → 验证 HimmPat 业务 MCP
  → HimmPat MCP（验证成功时优先）
  → Exa MCP（HimmPat 不可用时）
  → Codex 内置上网/浏览或用户分组检索式（前两者不可用时）
  → 输出候选文件和用户下载清单
  → 用户提供完整文件
  → 文件全文、著录项、日期和同族核验
  → 确定最接近现有技术
  → 区别特征、效果和实际技术问题分析
  → 其他文件的结合动机、成功预期和相反教导分析
  → 创造性检索分析
  → 申请文本接口
```

新颖性与创造性分开处理：新颖性不得拼接多件文件；创造性按每项独立权利要求分析。待证机理、结构和性能不得直接写成确定事实或权利要求特征。

## 目录

```text
SKILL.md
references/
  inventive-search-workflow.md
  inventive-search-analysis-template.md
```

- `SKILL.md`：Skill 入口、适用范围和关键约束。
- `references/inventive-search-workflow.md`：创造性检索和用户交接的详细工作流。
- `references/inventive-search-analysis-template.md`：案件目录中《创造性检索分析.md》的建议模板。

## 工具说明

### HimmPat

每次检索先验证 HimmPat 业务 MCP：优先调用 `product_patent_discovery`，确认 `initialize`、`tools/list` 和一次实际检索调用均成功。验证成功后优先使用 HimmPat MCP；只有业务 MCP 未注入、连接/业务调用失败或认证失败时，才降级到 Exa。网页会话与 API Bearer 密钥是不同路径；In-App Browser 仅用于页面级查看或用户明确要求的网页下载，不作为 MCP 可用性判断。Exa 也不可用时，可使用 Codex 内置上网/浏览工具；仍无法检索则输出可复制的分组检索式和候选文件清单，不把其他来源冒充为 HimmPat 结果。

API 密钥轮换时，在本机 Codex 配置的相关 `[mcp_servers.product_*]` 段落中，将 `headers.Authorization` 的 `Bearer <旧密钥>` 替换为新密钥，并重新加载 Codex MCP。多个端点共用同一密钥时一并替换；不要把真实密钥写入仓库、README、提交记录或对话。网页会话过期时，直接在 HimmPat In-App Browser 中重新登录，不能用更换 API 密钥替代网页登录。

### Exa

Exa 可用于发现专利、论文、学位论文、会议资料、预印本和技术报告等候选对比资料。摘要、高亮和提取文本只用于初筛；高价值结果即使无法直接打开或存在付费墙，也应输出标题、作者/申请人、公开编号或 DOI、原始 URL、命中特征、价值理由和待核实内容，交给用户取得全文。Exa 不可用时，可改用 Codex 内置上网/浏览工具，并注明实际渠道。

### 文档读取

含可提取文本的 DOCX、PDF、PPTX、XLSX 优先使用 Firecrawl AnyDoc 转为 Markdown；AnyDoc 不可用或转换失败时，改用 Python/OOXML 等方式读取。扫描件、图片型 PDF 或关键内容存在于图像中的 PDF，先暂停深入结论，请用户提供简略正文；确有必要时再逐页截图核对并记录页码。

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
