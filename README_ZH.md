# Quicker Connector - OpenClaw 技能

一个强大的 OpenClaw 技能，用于集成 Quicker 自动化工具。支持读取、搜索和执行 Quicker 动作，通过 AI 智能匹配自然语言需求。

[English README](README.md) | [GitHub Releases](https://github.com/awamwang/quicker-connector/releases)

---

## ✨ 核心功能

- 📊 **双数据源支持** - CSV 文件和 SQLite 数据库
- 🔍 **多字段搜索** - 按名称、描述、类型、面板搜索
- 🧠 **智能匹配** - AI 驱动的自然语言理解
- 🎯 **精准执行** - 同步/异步执行，支持参数传递
- 📈 **统计信息** - 完整的动作分类和面板分布
- 🔧 **编码自适应** - 自动检测 UTF-8/GBK 等编码
- 📤 **JSON 导出** - 一键导出完整动作列表

---

## 🚀 快速开始

### 安装方式

#### 方式 1: 通过 GitHub Release 安装（推荐）

```bash
# 下载最新版本
wget https://github.com/awamwang/quicker-connector/releases/download/v1.2.0/quicker-connector-1.2.0.tar.gz

# 解压
tar -xzf quicker-connector-1.2.0.tar.gz

# 复制到 OpenClaw 技能目录
cp -r quicker-connector/* ~/.openclaw/workspace/skills/quicker-connector/

# 重启 OpenClaw Gateway
openclaw gateway restart
```

#### 方式 2: 通过 ClawHub 安装

```bash
clawhub install quicker-connector
```

#### 方式 3: 通过 Git 安装（开发者）

```bash
git clone https://github.com/awamwang/quicker-connector.git ~/.openclaw/workspace/skills/quicker-connector
openclaw gateway restart
```

---

### 首次初始化

安装完成后，运行初始化向导：

```bash
python ~/.openclaw/workspace/skills/quicker-connector/scripts/init_quicker.py
```

按照提示操作：
1. 在 Quicker 软件中导出动作列表（工具 → 导出动作列表(CSV)）
2. 保存 CSV 文件到任意位置
3. 在向导中输入完整的 CSV 文件路径

配置将自动保存到 `config.json` 文件中。

---

### 基本使用示例

#### Python API 使用

```python
from scripts.quicker_connector import QuickerConnector

# 创建连接器（CSV 模式）
connector = QuickerConnector(source="csv")

# 读取所有动作
actions = connector.read_actions()
print(f"成功加载 {len(actions)} 个动作")

# 搜索动作
results = connector.search_actions("截图")
for action in results:
    print(f"- {action.name} (面板: {action.panel})")

# 智能匹配（AI 推荐）
matches = connector.match_actions("帮我翻译这段文字", top_n=3)
for m in matches:
    print(f"{m['action'].name} (匹配度: {m['score']:.2f})")

# 执行动作
result = connector.execute_action(
    action_id="42637f2a-f1e6-4bf9-b61a-bb22e2251c91",
    wait_for_result=True,
    timeout=10
)
print(f"执行结果: {'成功' if result.success else '失败'}")
if result.output:
    print(f"输出: {result.output}")
```

#### 自然语言交互

在 OpenClaw 中直接通过自然语言调用：

- "用 quicker 截图" - 搜索并推荐截图动作
- "帮我翻译这段文字" - 智能匹配翻译动作
- "列出所有 quicker 动作" - 获取完整列表和统计
- "quicker 搜索包含'搜索'的动作" - 搜索特定关键词

---

## 📊 数据结构

### QuickerAction（动作数据类）

| 字段 | 类型 | 说明 |
|------|------|------|
| `id` | str | 动作唯一标识符 |
| `name` | str | 动作名称 |
| `description` | str | 动作描述 |
| `action_type` | str | 动作类型（XAction/SendKeys/RunProgram 等） |
| `uri` | str | 执行 URI（quicker:runaction:xxx） |
| `panel` | str | 所属面板/分类 |
| `exe` | str | 关联程序名 |
| `create_time` | str | 创建时间 |
| `update_time` | str | 更新时间 |

### QuickerActionResult（执行结果）

| 字段 | 类型 | 说明 |
|------|------|------|
| `success` | bool | 是否执行成功 |
| `output` | str | 标准输出内容 |
| `error` | Optional[str] | 错误信息（失败时） |
| `exit_code` | Optional[int] | 退出码 |

---

## ⚙️ 配置选项

配置文件路径：`~/.openclaw/workspace/skills/quicker-connector/config.json`

```json
{
  "csv_path": "/root/.openclaw/workspace/data/QuickerActions.csv",
  "db_path": "C:\\Users\\Administrator\\AppData\\Local\\Quicker\\data\\quicker.db",
  "starter_path": "C:\\Program Files\\Quicker\\QuickerStarter.exe",
  "default_source": "csv",
  "auto_select_threshold": 0.8,
  "max_results": 10
}
```

### 设置说明

| 设置项 | 类型 | 默认值 | 说明 |
|--------|------|--------|------|
| `csv_path` | 字符串 | "" | Quicker 动作 CSV 文件路径 |
| `db_path` | 字符串 | "" | Quicker 数据库路径 |
| `starter_path` | 字符串 | "C:\\Program Files\\Quicker\\QuickerStarter.exe" | QuickerStarter.exe 路径 |
| `default_source` | 字符串 | "csv" | 默认数据源类型（csv/db） |
| `auto_select_threshold` | 浮点数 | 0.8 | 自动执行阈值（0.5-1.0） |
| `max_results` | 整数 | 10 | 最大返回结果数量 |

---

## 🔧 高级功能

### 导出为 JSON

将完整动作列表导出为 JSON 文件：

```python
connector.export_to_json("actions.json")
```

### 获取统计信息

```python
stats = connector.get_statistics()
print(f"总计: {stats['total']} 个动作")
print("类型分布:", stats['by_type'])
print("面板分布:", stats['by_panel'])
```

### 批量处理

```python
# 获取所有 XAction 类型动作
actions = connector.read_actions()
xaction_only = [a for a in actions if a.action_type == 'XAction']
print(f"找到 {len(xaction_only)} 个可执行动作")
```

---

## 📋 CSV 文件格式

Quicker 导出的 CSV 文件格式：

```csv
sep=,
Id,名称,说明,图标,类型,Uri,动作页,EXE,关联Exe,位置,大小,创建或安装时间,最后更新,来源动作
123,动作名称,动作说明,图标URL,XAction,quicker:runaction:123,默认页,,,0,0,2024-01-01 10:00:00,2024-01-01 10:00:00,
```

**重要字段说明**：
- `类型`：XAction、SendKeys、RunProgram 等
- `Uri`：执行 URI，格式为 `quicker:runaction:<动作ID>`
- `动作页`：动作所属面板/分类

---

## 🔒 安全特性

- ✅ **文件操作限制** - 仅访问用户指定路径
- ✅ **子进程控制** - 仅限 QuickerStarter.exe
- ✅ **无网络访问** - 不进行任何网络请求
- ✅ **无数据收集** - 不收集或传输用户数据
- ✅ **权限最小化** - 遵循最小权限原则

---

## 🐛 常见问题

### 问题 1: 文件不存在
**错误**: `FileNotFoundError: [Errno 2] No such file or directory`
**解决**: 检查 CSV 或数据库路径是否正确

### 问题 2: 编码错误
**错误**: 中文字符显示乱码
**解决**: 在设置中调整 `encodings` 顺序，优先使用 `utf-8-sig`

### 问题 3: QuickerStarter 未找到
**错误**: `QuickerStarter.exe not found`
**解决**: 手动配置 `starter_path` 设置项

### 问题 4: 动作执行失败
**错误**: `执行失败，错误信息: ...`
**解决**: 检查动作 ID 是否正确，确保 Quicker 软件正在运行

---

## 🏗️ 项目结构

```
quicker-connector/
├── SKILL.md                    # OpenClaw 技能文档
├── skill.json                  # 技能元数据配置
├── README.md                   # 项目说明文档（英文）
├── README_ZH.md                # 项目说明文档（中文）
├── LICENSE                     # MIT 许可证
├── CHANGELOG.md                # 版本历史记录
├── CONTRIBUTING.md             # 贡献指南
├── .gitignore                  # Git 忽略配置
├── scripts/
│   ├── quicker_connector.py    # 主连接器模块
│   ├── quicker_skill.py        # 技能接口类
│   ├── init_quicker.py         # 初始化向导
│   └── encoding_detector.py    # 编码检测工具
├── tests/
│   ├── test_quicker_connector.py # 主功能测试
│   └── test_quicker.py         # 辅助测试
└── examples/
    └── ocr_example.py          # OCR 使用示例
```

---

## 🧪 运行测试

```bash
# 进入技能目录
cd ~/.openclaw/workspace/skills/quicker-connector

# 运行测试套件
python tests/test_quicker_connector.py
```

---

## 📊 股市相关动作示例

你的 Quicker 中包含大量股市相关动作：

| 交易软件 | 动作数量 | 主要用途 |
|----------|----------|----------|
| **通达信** | 68 个 | 股票查询、交易策略、板块管理 |
| **同花顺** | 26 个 | F10、问财搜索、数据分析 |
| **东方财富** | 1 个 | 竞价功能 |
| **交易软件** | 18 个 | 交易操作、账号管理 |

**使用示例**：
- "用 quicker 查询股票" - 搜索股票查询动作
- "用 quicker 导出通达信数据" - 导出数据
- "用 quicker 同花顺 F10" - 查询公司信息

---

## 📄 许可证

MIT License - 详情见 [LICENSE](LICENSE) 文件

## 🤝 贡献指南

欢迎贡献！请阅读 [CONTRIBUTING.md](CONTRIBUTING.md) 了解如何参与开发。

---

## 🔗 相关链接

- [OpenClaw 文档](https://docs.openclaw.ai)
- [Quicker 官网](https://getquicker.net/)
- [ClawHub](https://clawhub.ai)
- [GitHub 仓库](https://github.com/awamwang/quicker-connector)

---

## 📝 版本历史

### v1.2.0 (2026-03-28)
- ✅ Advanced Skill Creator 优化
- ✅ 完整的 OpenClaw 规范兼容
- ✅ 自然语言触发支持
- ✅ 系统提示和思考模型
- ✅ 增强的设置选项
- ✅ GitHub 发布准备

### v1.1.0 (2026-03-27)
- ✅ 初始化向导
- ✅ 数据库支持
- ✅ 智能匹配功能
- ✅ 模糊搜索

### v1.0.0 (2026-03-27)
- ✅ 初始版本
- ✅ CSV 读取
- ✅ 多字段搜索
- ✅ 动作执行

---

**注意**: 本技能需要 Windows 操作系统和 Quicker 软件才能正常使用。