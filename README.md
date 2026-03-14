# Wing-Models

一个可复用的 OpenAI 兼容模型对话工作流技能包。

## 功能

- **多配置支持**: 使用 alias 管理多个 API 配置
- **多服务商兼容**: 支持 OpenAI、OpenRouter、Azure OpenAI、本地模型等
- **多模态输入**: 支持文件附件和 URL 作为输入
- **安全存储**: 凭证存储于本地 `.3rd.env` 文件
- **对话记录**: 自动保存对话输出为 Markdown 文件

## 安装

```bash
npm install --prefix <skill-dir>/scripts
```

## 快速开始

### 交互式配置（首次使用）

```bash
node <skill-dir>/scripts/wing_models.mjs
```

按提示依次输入：
1. Alias（配置别名）
2. Base URL（API 端点，如 `https://api.openai.com/v1`）
3. API Key
4. Model ID（模型标识）
5. Note（可选备注）

### 命令行调用

```bash
node <skill-dir>/scripts/wing_models.mjs --alias <alias> --prompt "你的问题"
```

### 从文件读取提示

```bash
node <skill-dir>/scripts/wing_models.mjs --alias <alias> --prompt-file prompt.txt
```

### 添加附件

```bash
node <skill-dir>/scripts/wing_models.mjs --alias <alias> --prompt "分析这个图片" --attachment image.png
node <skill-dir>/scripts/wing_models.mjs --alias <alias> --prompt "读取这个URL" --attachment https://example.com/file.pdf
```

## 配置文件

配置存储在工作目录的 `.3rd.env` 文件中：

```env
WING_MODELS_DEFAULT_ALIAS=my-alias
WING_MODELS_PROFILE_SET=[{"alias":"my-alias","baseURL":"https://api.openai.com/v1","apiKey":"sk-xxx","modelId":"gpt-4o","note":""}]
```

## 支持的服务商

| 服务商 | Base URL 示例 |
|--------|---------------|
| OpenAI | `https://api.openai.com/v1` |
| OpenRouter | `https://openrouter.ai/api/v1` |
| Azure OpenAI | `https://YOUR_RESOURCE.openai.azure.com/openai/deployments/YOUR_DEPLOYMENT` |
| Ollama | `http://localhost:11434/v1` |

## 命令参数

| 参数 | 说明 |
|------|------|
| `--alias <name>` | 指定配置别名 |
| `--prompt "<text>"` | 直接传入提示文本 |
| `--prompt-file <path>` | 从文件读取提示 |
| `--attachment <path-or-url>` | 添加附件（可重复） |
| `--list-aliases` | 列出所有配置 |
| `--default-alias <name>` | 设置默认别名 |
| `--save-env` | 强制保存配置 |
| `--help` | 显示帮助 |

## 输出文件

- `*-dialogue.md`: 对话记录（问题 + 回答）
- `*-attachment-<n>.<ext>`: 输出附件
- `*-input-attachment-<n>.<ext>`: 输入附件副本

## 安全说明

- API Key 不会出现在日志或终端输出中
- `.3rd.env` 文件使用限制权限存储（600）
- 首次非交互运行需显式指定 `--alias`

## 文件结构

```
wing-models/
├── SKILL.md              # 技能定义
├── scripts/
│   ├── wing_models.mjs   # 主脚本
│   └── package.json      # 依赖配置
├── references/
│   └── regression-checklist.md
├── .3rd.env.template     # 配置模板
└── LICENSE               # GPL-3.0
```

## 许可证

[GPL-3.0-or-later](LICENSE)