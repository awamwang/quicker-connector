# quicker-connector

A powerful OpenClaw skill for integrating with Quicker automation tool. Read, search, and execute Quicker actions with AI-powered natural language matching.

## ✨ Features

- 📊 **Dual Data Sources**: Support both CSV and SQLite database
- 🔍 **Smart Search**: Multi-field search (name, description, type, panel)
- 🧠 **AI Matching**: Natural language understanding with keyword extraction
- 🎯 **Precise Execution**: Sync/async action execution with parameter support
- 📈 **Statistics**: Complete action categorization and panel distribution
- 🔧 **Encoding Adaptive**: Auto-detect UTF-8/GBK and other encodings
- 📤 **JSON Export**: One-click export of complete action list

## 🚀 Quick Start

### Installation

```bash
# Clone this repository
git clone https://github.com/yourusername/quicker-connector.git

# Install dependencies (none required - Python stdlib only)
```

### Initialization

```bash
# Run the initialization wizard
python scripts/init_quicker.py
```

Follow the prompts to:
1. Export your Quicker action list (CSV format)
2. Provide the CSV file path
3. Configuration will be saved to `config.json`

### Basic Usage

```python
from scripts.quicker_connector import QuickerConnector

# Create connector
connector = QuickerConnector(source="csv")

# Read all actions
actions = connector.read_actions()
print(f"Loaded {len(actions)} actions")

# Search actions
results = connector.search_actions("screenshot")
for action in results:
    print(f"- {action.name}")

# Smart match
matches = connector.match_actions("help me translate this text", top_n=3)
for m in matches:
    print(f"{m['action'].name} (score: {m['score']:.2f})")

# Execute action
result = connector.execute_action(action_id="xxxx", wait_for_result=True)
print(f"Result: {result.success}, Output: {result.output}")
```

## 📊 Data Structure

### QuickerAction

```python
@dataclass
class QuickerAction:
    id: str                    # Unique identifier
    name: str                  # Action name
    description: str           # Description
    action_type: str           # Type: XAction, SendKeys, etc.
    uri: str                   # Execution URI
    panel: str                 # Panel/category
    # ... and more fields
```

### QuickerActionResult

```python
@dataclass
class QuickerActionResult:
    success: bool              # Success status
    output: str                # Standard output
    error: Optional[str]       # Error message
    exit_code: Optional[int]   # Exit code
```

## 🛠️ Configuration

Configuration is stored in `config.json`:

```json
{
  "csv_path": "/path/to/QuickerActions.csv",
  "initialized": true,
  "default_source": "csv"
}
```

### Settings

| Setting | Type | Default | Description |
|---------|------|---------|-------------|
| `csv_path` | string | "" | Path to Quicker actions CSV |
| `db_path` | string | "" | Path to Quicker database |
| `starter_path` | string | "C:\\Program Files\\Quicker\\QuickerStarter.exe" | QuickerStarter.exe path |
| `auto_select_threshold` | float | 0.8 | Auto-execution threshold |
| `max_results` | int | 10 | Maximum search results |

## 🔧 Advanced Features

### JSON Export

```python
connector.export_to_json("actions.json")
```

### Statistics

```python
stats = connector.get_statistics()
print(f"Total: {stats['total']}")
print("Types:", stats['by_type'])
print("Panels:", stats['by_panel'])
```

### Batch Processing

```python
actions = connector.read_actions()
xaction_only = [a for a in actions if a.action_type == 'XAction']
```

## 🧪 Testing

```bash
# Run test suite
python tests/test_quicker_connector.py
```

## 📋 CSV Format

Quicker exported CSV format:

```csv
sep=,
Id,名称,说明,图标,类型,Uri,动作页,EXE,关联Exe,位置,大小,创建或安装时间,最后更新,来源动作
123,动作名称,动作说明,图标URL,XAction,quicker:runaction:123,默认页,,,0,0,2024-01-01 10:00:00,2024-01-01 10:00:00,
```

## 🏗️ Project Structure

```
quicker-connector/
├── scripts/
│   ├── quicker_connector.py    # Main connector module
│   ├── quicker_skill.py        # Skill interface class
│   ├── init_quicker.py         # Initialization wizard
│   └── encoding_detector.py    # Encoding detection utility
├── tests/
│   ├── test_quicker_connector.py # Main test suite
│   └── test_quicker.py         # Additional tests
├── examples/
│   └── ocr_example.py          # OCR usage example
├── SKILL.md                    # OpenClaw skill documentation
├── skill.json                  # Skill metadata
├── LICENSE                     # MIT License
├── README.md                   # This file
└── config.json                 # Generated configuration
```

## 🔒 Security

- All file operations are restricted to user-specified paths
- Subprocess calls limited to QuickerStarter.exe
- No network access
- No sensitive data collection

## 🐛 Troubleshooting

| Problem | Solution |
|---------|----------|
| `FileNotFoundError` | Check CSV/DB path is correct |
| Encoding errors | Adjust `encodings` order in settings |
| QuickerStarter not found | Configure `starter_path` manually |
| Action execution fails | Check action ID and permissions |

## 📄 License

MIT License - see [LICENSE](LICENSE) file for details

## 🤝 Contributing

1. Fork the repository
2. Create a feature branch
3. Add tests for new functionality
4. Submit a pull request

## 🔗 Links

- [OpenClaw Documentation](https://docs.openclaw.ai)
- [Quicker Website](https://getquicker.net/)
- [ClawHub](https://clawhub.ai)

---

**Note**: This skill requires Windows and Quicker software to function properly.