# 🧪 Req Flow Agent — AI-Powered Test Automation Workflow

From **functional specifications** to **executable Robot Framework tests** — powered by [OpenCode](https://opencode.ai/) skills and [rf-mcp](https://github.com/manykarim/rf-mcp).

Req Flow Agent is a set of OpenCode AI skills that automate the full test development lifecycle:

1. **Analyze** functional specifications and generate structured test cases
2. **Convert** test cases into production-ready Robot Framework scripts
3. **Validate** generated scripts using rf-mcp tools

---

## 🛠️ Prerequisites & Installation

### Prerequisites

- Python 3.10+
- [uv](https://docs.astral.sh/uv/) package manager
- [OpenCode](https://opencode.ai/)

### Installation

```bash
# Install uv if you don't have it
curl -LsSf https://astral.sh/uv/install.sh | sh

# Clone the repository
git clone https://github.com/patrikzakovic/req-flow-agent.git
cd req-flow-agent

# Create virtual environment and install dependencies
uv sync

# Initialize Playwright browsers (required for Browser Library)
uv run rfbrowser init
```

### 🔌 rf-mcp Setup

This project uses [rf-mcp](https://github.com/manykarim/rf-mcp) (RobotMCP) as an MCP server to provide Robot Framework intelligence to the AI skills:

- **`robot-get_environment_details`** — verify RF version and installed libraries
- **`robot-get_library_documentation`** — look up available keywords
- **`robot-get_keyword_documentation`** — get exact keyword signatures
- **`robot-get_file_imports`** — validate generated `.robot` file imports

Start the rf-mcp server:

```bash
uv run -m robotmcp.server --transport http --host 127.0.0.1 --port 8000
```

The project connects to rf-mcp via HTTP transport, configured in [opencode.json](opencode.json):

```json
{
  "mcp": {
    "local": {
      "type": "remote",
      "url": "http://localhost:8000/mcp"
    }
  }
}
```

---

## ✨ Quick Start

Once installed and rf-mcp is running, launch OpenCode:

```bash
uv run opencode
```

**Generate test cases from a spec:**

```
/test-analyst Analyze the functional specification in specs/my_feature_fs.md.
Generate 10-15 mixed test cases covering happy paths, edge cases, and error scenarios.
```

**Convert test cases to Robot Framework scripts:**

```
/test-developer Convert test cases from artifacts/my_feature_tests.md
into executable Robot Framework scripts using Browser library.
```

---

## 📁 Project Structure

```
req-flow-agent/
├── .opencode/
│   └── skills/
│       ├── test-analyst/          # Skill: spec → test cases
│       │   ├── SKILL.md
│       │   └── references/
│       └── test-developer/        # Skill: test cases → .robot scripts
│           ├── SKILL.md
│           └── references/
├── specs/                         # Functional specifications (input)
├── artifacts/                     # Generated test cases & scripts (output)
├── opencode.json                  # OpenCode + MCP configuration
├── pyproject.toml                 # UV/Python dependencies
└── README.md
```

---

## 🔧 Skills

### `/test-analyst` — Test Case Generator

Analyzes functional specifications and generates structured test cases.

| Feature | Description |
|---|---|
| **Input** | Functional specifications (`.md`, `.pdf`, `.docx`, `.txt`) in `specs/` |
| **Output** | Test cases in Markdown or CSV format in `artifacts/` |
| **Test Types** | Acceptance, API, UI, Integration, Error Handling, Mixed |
| **Languages** | English, Slovak |
| **Count Ranges** | 1–2 (smoke) up to 40+ (exhaustive) |
| **Refinement** | Iterative feedback via follow-up chat messages |

### `/test-developer` — Robot Framework Script Generator

Converts structured test cases into executable `.robot` files.

| Feature | Description |
|---|---|
| **Input** | Test cases in `.md` or `.csv` format from `artifacts/` |
| **Output** | `.robot` test suites and `.resource` files in `artifacts/` |
| **Library** | Browser (Playwright-based) |
| **Validation** | Uses rf-mcp tools to verify keywords and imports |

---

## 🎪 Example Workflow

### 1. Write a Functional Specification

Place your spec in the `specs/` folder (e.g., `specs/login_fs.md`):

```markdown
# Functional Specification: Login

## Successful Login
- User enters valid credentials
- System redirects to home page

## Failed Login
- Invalid credentials show error message
```

### 2. Generate Test Cases

```
/test-analyst Analyze specs/login_fs.md. Generate 3-8 acceptance test cases.
```

Output: `artifacts/login_acceptance_tests.md`

### 3. Generate Robot Framework Scripts

```
/test-developer Convert artifacts/login_acceptance_tests.md to Robot Framework scripts.
```

Output: `artifacts/login.robot`

### 4. Run Tests

```bash
uv run robot --outputdir results artifacts/login.robot
```

---

## 🤝 Contributing

1. Fork the repository
2. Create a feature branch
3. Make your changes
4. Submit a pull request

---

## 📄 License

Apache 2.0 License — see [LICENSE](LICENSE) for details.

---

Built with [OpenCode](https://opencode.ai/) and [rf-mcp](https://github.com/manykarim/rf-mcp) for the Robot Framework and AI automation community.
