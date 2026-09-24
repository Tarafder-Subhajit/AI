# 1. Introduction to Claude Code #
Claude Code is a coding agent that runs in your terminal, your IDE, your browser, your phone, and on Anthropicʼs cloud while your laptop is closed. Not a chat window. Not a copilot. A full agent — it
reads files, runs commands, edits code, opens browsers, queries databases, schedules itself, and ships features.
Four things make it different from every other AI coding tool:
1. It runs in your shell. Not a sandbox. It sees your real repo, your real env vars, your real node_modules
2. Itʼs extensible at every layer. Skills, subagents, hooks, MCP, plugins, routines — everything is a markdown file or a JSON config.
3. It composes. A skill calls a subagent. A hook blocks a tool. An MCP server exposes tools. A routinefires the whole stack on a schedule.
4. It runs everywhere. Terminal, VS Code, JetBrains, Desktop app, Web (claude.ai/code) , mobile via the Claude iOS app. Same claude CLI under the hood.

# 2. Install & First Run #
**macOS / Linux / WSL — recommended (auto-updates)**  
curl -fsSL https://claude.ai/install.sh | bash

> After Installation, create an account in Anthropic Account. You need to spend atleast $5 to create account.
> Alternatively, we can use Ollama. Download Ollama after installing Claude.
> <img width="1048" height="631" alt="image" src="https://github.com/user-attachments/assets/990a7d7b-1b6d-4afc-8a6c-7c81eacdc03a" />
> Make sure that you are using the lastest version of claude. Exit the claude and use "$claude Update" in terminal.
> To verify claude version use: $Claude Doctor

# 3. The Core Loop or Agentic Loop (How it works) #
### Claude is LLM + Tools ###
Once inside claude, the loop is:
1. You type a prompt (or paste a task, drop a file).
2. Claude reads, thinks, calls tools (Read, Edit, Bash, Glob, Grep, WebFetch…).
3. You watch tool calls scroll by, approving anything that needs permission.
4. You correct, redirect, or ship.

### The keys you need ###
| **Key** | **Action** |
|------|------|
| Enter | Send prompt |
| Shift+Enter | Newline in prompt |
| Esc | Interrupt Claude (stop a tool / response) |
| Esc Esc | Open Checkpoint menu — restore code+conversation, conversation only, code only, or summarize-from-here (see §17) |
| Shift+Tab | Cycle permission modes (default → acceptEdits → plan → auto) |
| Ctrl+B | Send running task to background |
| Ctrl+O | Toggle verbose transcript (see raw tool I/O) |
| Ctrl+U | Clear input buffer |
| Ctrl+L | Redraw screen |
| Ctrl+C | Quit |
| ! (start of line) | Run as bash directly, capture output |
| # (start of line) | Save line to CLAUDE.md memory |
| @filename | Inject a file into the prompt |
| / | Slash-command menu |

