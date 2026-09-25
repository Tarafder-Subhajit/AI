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

# 4. Permission Modes #
| Mode | Auto-approves | Use it for |
|------|------|------|
| default | Reads only | Starting cold, sensitive repos |
|acceptEdits| Reads + Edits + safe bash (cp , sed , mkdir , mv) | Active iteration on code |
| plan | Reads only — drafts a plan, asks before executing | Big refactors, “what would you do” |
| auto | Classifier-driven — auto-approves safe, escalates risky (Max-only on Opus 4.7 | Long-running autonomous sessions |
| DontAsk| Suppresses prompts entirely — denies what isnʼt pre-approved instead of asking | Headless / piped runs that mustnʼt block on prompts |
| bypassPermissions | Everything. No checks. | Disposable VMs / containers only |

### - Configure permission ###
Claude --permission-mode plan
### - Inspect the Auto-mode classifier ###
claude auto-mode  
Run this command in your regular terminal, outside or before entering an interactive Claude Code session.
This command is used to inspect the effective Auto-mode configuration. It helps you understand what the classifier currently considers trusted, including configured repositories, domains, cloud resources, and other environment boundaries.
In Auto mode, Claude Code does not ask you to approve every routine action. Instead, a separate safety classifier reviews relevant actions before they run. It can block actions that appear destructive, irreversible, or outside the trusted environment.
### - Open permission settings ###
/permissions  
The /permissions command opens the permissions interface. Here, you can inspect and manage the rules controlling which tools and commands Claude can use.
### - Cycle modes using Shift + Tab ###
During a live Claude Code session, press: **Shift + Tab** lets you change Claude’s level of autonomy without ending the session or restarting Claude Code.
### - .claude directory ###
<img width="942" height="48" alt="image" src="https://github.com/user-attachments/assets/ad69ac35-ceca-4842-b949-31ae16e0abea" />  
The .claude directory stores project-specific configuration, permission rules, skills, commands, hooks and agents.  
It can exist at two main levels:  

1. Project-level .claude  

```text
your-project/
├── .claude/
│   ├── settings.json
│   ├── settings.local.json
│   ├── rules/
│   ├── skills/
│   ├── commands/
│   └── agents/
├── CLAUDE.md
└── src/
```

This configuration applies only to the current project. Shared project settings can be committed to Git so that other developers receive the same configuration.  

2. User-level root .claude  
C:\Users\Subhajit\.claude [WINDOWS]  
~/.claude [Linux]
User-level settings apply to all your projects on the current computer.

Important files inside .claude are:
- settings.json : Contains project settings shared with the team, such as: Permission rules, Hooks, Plugins, Environment variables, Claude Code behavior. This file can normally be committed to Git.
- settings.local.json: Contains your personal settings for the current project, such as locally approved commands or personal overrides.

<img width="897" height="192" alt="image" src="https://github.com/user-attachments/assets/4e670e85-4448-4420-908a-166b12949d7b" />

# 5. claude.md #
- CLAUDE.md is a Markdown instruction file that tells Claude Code how it should understand and work with your project. Claude Code loads these instructions at the beginning of each applicable session, so you do not need to repeat the same project information every time.
- It is placed inside .claude directory
- To initialize claude.md:
  1. go to your project folder in terminal
  2. start claude session with **/claude**
  3. run **/init** : The command examines the project structure and generates a starter CLAUDE.md. The generated file can include detected build commands, test commands, architecture, dependencies, and coding conventions.
- **Where they live**
  1. ~/.claude/CLAUDE.md : This is your user-level instruction file. Instructions written here apply to you across all projects on the current computer. (Your preferences across every project)
  2. ./CLAUDE.md : This is the main shared project instruction file. It can describe the project architecture, common commands, coding standards, testing requirements, and team conventions. Claude loads project instructions at the beginning of the session. (Shared instructions for everyone in this repository)
  3. ./CLAUDE.local.md : This represents personal instructions for one repository only. (Your personal instructions for this repository, if supported)
  4. .claude/rules/*.md : The * means that the directory can contain multiple Markdown rule files like python.md, terraform.md etc
- **Pull CLAUDE.md from additional directories**  
  - Normally, CLAUDE.md contains project-specific instructions that Claude Code loads into its context. You can give Claude access to another directory by using --add-dir
  ```
  claude --add-dir ../shared-libs
  ```
  - However, adding the directory does not necessarily mean its CLAUDE.md instructions will be loaded. To enable that behavior, set this environment variable:
  ```
  export CLAUDE_CODE_ADDITIONAL_DIRECTORIES_CLAUDE_MD=1
  ```
  - So Claude can now use:
  ```
  my-project/CLAUDE.md
  ../shared-libs/CLAUDE.md
  ```
  - Instead of specifying --add-dir every time, you can define additional directories in your Claude settings. For example, in .claude/settings.json:
    ```
    {
      "additionalDirectories": [
        "../shared-libs",
        "../shared-config"
      ]
    }
    ```
    Then set the environment variable before launching Claude:
    ```
    export CLAUDE_CODE_ADDITIONAL_DIRECTORIES_CLAUDE_MD=1
    claude
    ```
    # 6. Claude Auto Memory #
    Suppose i haven't created claude.md  nor i have created any .claude directory to give project settings, now if i do some progress in Claude then exits it. After revisiting claude, will my progress be lost ??????  
    **ANSWER: NO!**
    ```
    claude --resume
    ```
    This is Auto-memory which is present in .claude of root user directory
    ```
    /users/<user-name>/.claude/sessions
    ```
    # 7. Live Portfolio Project #
    1. create the project dir
       ```
       mkdir projects
       claude
       ```
    2. [OPTIONAL] Sometimes the claude's settings is not optimum, so fix it.
       ```
       can u fix the claude settings in this folder?
       ```
    3. 
    
    
  





