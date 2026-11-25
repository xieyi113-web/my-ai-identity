---
name: docx
description: "专业 Word 文档（.docx / .dotx）制作、编辑、修订留痕（Redlining）、批注与内容提取技能。适用于新建带目录、标题层级、页码、抬头或复杂表格的专业 Word 文档，编写 docx-js 或 python-docx 生成脚本，修改既有 Word 文件与模板，处理修订模式与批注，以及提取重组文档内容。当用户提及 Word 文档、.docx、.dotx、合同修订留痕、Word 模板或要求以 Word 格式交付报告、函件、备忘录时主动调用。"
---

# docx

基于官方 `docx` 技能规范提炼的 Word 文档（`.docx` / `.dotx`）全流程制作、编辑与质检指南，涵盖 `docx-js` 编码避坑、OOXML 底层修改、合同修订留痕（Tracked Changes）、批注锚定及交付核查规范。

## When to Use

- 用户要求新建专业排版的 `.docx` Word 文档（含目录、多级标题、页眉页脚、页码、抬头或规范表格）。
- 用户需要基于既有 `.docx` 或 `.dotx` 模板进行内容替换、图片增改、结构重组或提取全文。
- 用户需要对 Word 合同或公文执行修订留痕（Redlining）、接受修订或添加批注（Comments）。
- 用户要求将报告、函件、备忘录或规章制度交付为可编辑的 Word 格式。

## Steps

### 1. 按任务类型选择技术路径

- **新建文档**：优先编写 `docx`（npm / `docx-js`）脚本生成；在仅支持 Python 的环境中，使用 `python-docx` 并遵循同等排版与防错规范。
- **修改既有文档或模板**：`.docx` 本质为 XML 压缩包，`docx-js` 无法打开既有文件。标准路径为：解压压缩包 -> 原地编辑 `word/document.xml` -> 重新打包校验。旧版 `.doc` 文件必须先转换为 `.docx`。
- **读取与提取内容**：本地可用 `pandoc -t markdown file.docx`；Python 环境逐段逐表读取全文及批注，确保无遗漏。

### 2. `docx-js` 新建文档核心避坑清单

- **页面尺寸与方向**：默认 A4；美标 Letter 设为 `width: 12240, height: 15840`（单位 DXA，1440 DXA = 1 英寸）。横向页面传纵向尺寸并设 `orientation: PageOrientation.LANDSCAPE`。
- **表格双宽度强制声明**：必须同时在表格设 `columnWidths` 且在每个单元格设 `width`，单位统一用 `WidthType.DXA`（`PERCENTAGE` 在 Google Docs 中会错乱），各列宽之和等于表格总宽。
- **表格底纹**：必须使用 `ShadingType.CLEAR`，严禁使用 `SOLID`（会渲染为纯黑底色）。
- **列表与换行**：严禁手写 `•` 符号，用 `numbering` 配置与 `LevelFormat.BULLET`；严禁使用 `\n` 换行，应拆分为独立 `Paragraph`。
- **图片与分页符**：`ImageRun` 必须显式指定 `type`（如 `"png"`、`"jpg"`）；`PageBreak` 必须嵌套在 `Paragraph` 内部。
- **标题层级与目录（TOC）**：在 `styles.default` 中为用到的每一级标题显式配置 `font`、`spacing: { before, after }` 及 `keepNext: true`。目录须用内置 `HeadingLevel.*`，自定义标题须设 `outlineLevel`。
- **分割线与点状前导符**：严禁用表格充当水平分割线，改用段落底部边框；同行右对齐或点状前导符使用 `TextRun` 内的 `PositionalTab`，严禁手打空格或句点。

### 3. 修改既有文档与 OOXML 底层规范

- **安全解压**：使用 Python `zipfile.ZipFile` 解压外部 `.docx`，避免使用系统 `unzip`（防恶意软链接）。
- **合并碎片化文本节点**：检索替换前先合并相邻且格式完全一致的 `<w:r>` 节点，防止 Word 拆分文本导致漏检。
- **原地编辑与打包**：直接编辑 `word/document.xml`，严禁格式化缩进（pretty-print）。重新打包须进入解压目录内部执行，打包前删除旧目标文件；交付前清除 ZIP 空目录项（如 `zip -d output.docx '*/'`），防止云端协作报错。

### 4. 修订留痕（Tracked Changes / Redlining）规范

- **增删标记**：新增用 `<w:ins>`，删除用 `<w:del>`，完整填写 `w:id`、`w:author`、`w:date`。
- **删除文本标签**：`<w:del>` 内部文本必须放在 `<w:delText>` 中，严禁使用 `<w:t>`。
- **整段删除规则**：删除段落标记（`<w:pPr><w:rPr><w:del .../></w:rPr></w:pPr>`）表示与下段合并；彻底删除整段须同时标记段落删除及段内全部 `<w:r>`，且 `<w:del/>` 须排在 `<w:rPr>` 其他子节点之前。
- **留痕完整性**：避免遗漏未包裹 `<w:ins>` / `<w:del>` 的直接修改。

### 5. 批注（Comments）规范

- 批注涉及 6 个关联文件（`comments.xml`、`commentsExtended.xml`、`commentsIds.xml`、`commentsExtensible.xml`、关系文件及内容类型声明），并必须在 `word/document.xml` 中放置 `<w:commentRangeStart>`、`<w:commentRangeEnd>` 与 `<w:commentReference>` 锚点。

### 6. 交付前质量核查（QA）

- 确认表格均采用 DXA 双宽度、无 `SOLID` 底纹、无 `\n` 硬换行、无 ZIP 冗余目录项。
- 具备本地渲染环境时，通过 LibreOffice 转 PDF 并用 `pdftoppm` 转为逐页图片，核查分页截断、表格跨页错位与标题孤行。
