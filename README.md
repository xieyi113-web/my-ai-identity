# 个人 AI 身份与协作规范库（my-ai-identity）

本仓库用于集中维护个人工作背景、行文风格、数据处理规范及专属技能，实现多 AI 平台“单点维护、全端复用”，避免切换大模型时重复交代背景与格式要求。

---

## 一、 目录结构说明

| 文件 / 目录 | 核心用途 | 适用场景 |
| :--- | :--- | :--- |
| `README.md` | 仓库总览与多平台接入操作指南 | 日常查阅与新平台配置 |
| `PROFILE.md` | 脱敏版个人背景、岗位角色与专业领域 | 需要深度行业语境的项目知识库 |
| `STYLE.md` | 逻辑表达、公文文风、排版及数据核查规范 | 正式报告、公文起草与文件分析 |
| `BOOTSTRAP.md` | 500 字以内精简版“一键启动包” | 网页端 AI 自定义指令 / 新对话开场 |
| `skills/` | 专属技能库（存放 `pptx.md`、`docx.md`、`xlsx.md` 等） | 制作演示文稿、Word 公文或 Excel 模型时按需调用 |

---

## 二、 各 AI 平台接入步骤

### 1. Claude（网页端 / 桌面端 Projects）
适合深度文档处理与长周期项目协作，支持直接挂载完整规范。

1. 进入 Claude 左侧栏 **Projects**，点击 **Create Project** 新建项目（如命名“日常法务与风控协作”）。
2. 在项目右侧 **Project knowledge** 区域，点击 **Add content**：
   - 若已授权 GitHub，选择 **GitHub** 直接关联本私有仓库中的 `PROFILE.md` 与 `STYLE.md`。
   - 若未授权 GitHub，直接将 `PROFILE.md` 与 `STYLE.md` 下载后上传为项目文件。
3. （可选）在 **Set project instructions** 中粘贴 `BOOTSTRAP.md` 内容作为全局指令。
4. 仓库更新后，在 Project 中点击同步（Sync）或重新上传对应文件即可。

### 2. ChatGPT（网页端 / 桌面端）
支持“全局自定义指令”与“项目级知识库”两种模式。

- **全局生效（推荐日常使用）**：
  1. 点击左下角个人头像 -> **Customize ChatGPT（自定义 ChatGPT）**。
  2. 将 `BOOTSTRAP.md` 中的【身份与背景】粘贴至第一个输入框，将【核心原则与文风】【排版与数据核查】粘贴至第二个输入框。
  3. 勾选 **Enable for new chats** 并保存。
- **项目级深度配置（Plus / Team 用户）**：
  1. 在左侧栏新建 **Project**，在项目文件区上传 `PROFILE.md`、`STYLE.md` 及常用 `skills/` 文件。

### 3. Cursor / Claude Code / Windsurf（本地桌面端）
适合本地处理代码、批量脚本或自动化生成 PPT/Word/Excel 文件，同步成本最低。

1. 在本地电脑执行命令克隆仓库：
   ```bash
   git clone [https://github.com/](https://github.com/)<你的用户名>/my-ai-identity.git
