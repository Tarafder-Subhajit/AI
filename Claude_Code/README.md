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
    3. Enable Plan mode
       ```
       /plan
       ```
       ```
       Lets plan for a portfolio website that will have my projects showcased from my github https://github.com/Tarafder-Subhajit - this will be running on a docker container, nginx will serve the web page and the website will be running via ngrok. I dont need backend for now. Code it in such a way that it has phase wise development, where later phases will have backend and database connection as well. Ask me clarifying questions. Create tasks.md that will track the tasks you will be planning to do and create a folder called decisions/ where all the decisions made by you will be tracked so that we can do context management easily.
       ```
       <img width="1292" height="542" alt="image" src="https://github.com/user-attachments/assets/9d3d7b19-7039-49dc-a4a1-62709162b31a" />
       <img width="1301" height="558" alt="image" src="https://github.com/user-attachments/assets/bf3f5ed0-2f28-4909-9ac2-8e54eed1ce15" />
       <img width="1292" height="497" alt="image" src="https://github.com/user-attachments/assets/dad7266a-166e-483c-b8a8-aba54fa6f034" />
       <img width="1280" height="492" alt="image" src="https://github.com/user-attachments/assets/1ee80231-0e5e-4b20-82d8-141f255c579b" />

       ***NOTE***: We will get an error saying "**MKDIR was denied!**" because we have mentioned in settings.json previosly to ask before making a folder.
       So,
       ```
       Can you allow creating folder in project settings so that the background tools don't get access denied.
       ```
    4. Do the following:  
       <img width="952" height="168" alt="image" src="https://github.com/user-attachments/assets/9e6963e5-a0cb-4659-8b15-ceb923d39191" />  
       a. copy the .env.example to .env  
       b. vim .env  
       c. provide github token (create it) & provide ngrok auth token (go to ngrok , login & create it)

    5. Go to claude
       ```
       I have copied the .env. Now do the docker compose and make the application run.
       ```
# 7. Settings.json #  
The control panel. Everything configurable lives here  
```
{
  "$schema": "https://json.schemastore.org/claude-code-settings.json",
  "model": "auto",
  "outputStyle": "Default",
  "includeCoAuthoredBy": true,
  "tui": "default",
  "autoScrollEnabled": true,
  "disableSkillShellExecution": false,
  "permissions": {
    "defaultMode": "acceptEdits",
    "allow": [
      "Bash(npm run *)",
      "Bash(git *)",
      "Bash(pnpm *)",
      "Read(./**)"
    ],
    "deny": [
      "Read(./.env*)",
      "Read(./secrets/**)",
      "Bash(curl *)",
      "Bash(rm -rf *)"
    ],
    "ask": [
      "Bash(git push *)",
      "Bash(npm publish *)"
    ],
    "additionalDirectories": ["../shared-libs/"]
  },
  "env": {
    "NODE_ENV": "development",
    "DISABLE_AUTOUPDATER": "0",
    "SLASH_COMMAND_TOOL_CHAR_BUDGET": "8192",
    "ENABLE_PROMPT_CACHING_1H": "1"
  },
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Edit",
        "hooks": [
          { "type": "command", "command": "prettier --write \"$CLAUDE_FILE_PATHS\"" }
        ]
      }
    ]
  },
  "statusLine": {
    "type": "command",
    "command": "~/.claude/statusline.sh"
  },
trainwithshubham.com 15
"enabledPlugins": {
"python-library-complete@python-library-dev": true
}
}
```
1. Schema: Detect invalid settings, Show autocomplete suggestions, Highlight JSON errors, Explain supported properties
2. **"model": "auto"** : This tells to select an appropriate model automatically rather than forcing a specific model. Anthropic-side router picks Opus/Sonnet/Haiku per turn (Max only)
3. **"outputStyle": "Default"**: Default means Claude uses its standard response style instead of a custom output style. custom output style might tell Claude to Give very short responses, Behave like a tutor, Explain code step by step etc
4. **"tui": "default"**: This tells Claude to use its normal terminal interface.
5. **"autoScrollEnabled": true**: This allows the terminal output to automatically scroll as Claude generates responses or tool output.

# 8. Hooks #
Suppose I want to review the above code, so i wont be giving prompts like " Can you review my code? ".  
Instead I will use hooks by configuring settings.json.  
Hooks are automation points that run automatically at specific stages of Claude Code's lifecycle.  
```
/hooks
```
<img width="700" height="736" alt="image" src="https://github.com/user-attachments/assets/8fc92422-90fb-41fa-a699-38c395ecdb2a" />

Or we can directly give prompts like:  
```
can you create a hook for FileChanged - whenever there is a file change, you run a subagent that review the changed file code. This subsgent should reside in .claude repo locally. Also use the best coding and code review practices for this subagent.
```
NOTE: it might ask you to restart claude.  
Verify: Go to your project, go to .claude directory & open settings.local.json then you will see the hooks
<img width="500" height="537" alt="image" src="https://github.com/user-attachments/assets/36b249a5-ba68-4623-80d5-542950548ace" />

# 9. Slash Commands #
It is like a menu of commands that clause uses.  
<img width="602" height="872" alt="image" src="https://github.com/user-attachments/assets/89b1be44-7aab-4544-bb5d-a68f067c625c" />
# 10. Custom Slash Commands & Skills #
## Custom Slash Command ##
A custom slash command is a reusable prompt stored in a Markdown file. E.g. Review the Terraform files for security problems, hardcoded credentials, incorrect resource configuration, and missing tags.
We can create:  
```
/check-terraform
```
Project Level Custom Command:  
```
your-project/
├── .claude/
│   └── commands/
│       └── deploy-check.md
├── CLAUDE.md
└── src/
```
Creating a Custom Slash Command:  
```
mkdir -p .claude/commands
vim .claude/commands/deploy-check.md
```
Add the following instructions:
```
Review the project before deployment.

Check the following:

1. Verify that all tests pass.
2. Check for hardcoded credentials.
3. Review environment variables.
4. Validate the Dockerfile.
5. Review Kubernetes manifests.
6. Check whether rollback instructions exist.
7. Report critical, warning, and informational findings separately.
```
Now open Claude Code in the project and run:
```
/deploy-check
```
## Skill ##
A skill is a reusable package of instructions that teaches Claude Code how to perform a particular workflow.  
In the current Claude Code system, custom commands have been merged into skills.  
Both of these can create the same /deploy command:  
```
.claude/commands/deploy.md
```
and
```
.claude/skills/deploy/SKILL.md
```
So, Custom slash command = Older and simpler structure  
Skill = Newer and more powerful structure

SKILL.md:  
```
---
name: commit
description: Create a conventional commit with proper scope and body.
when_to_use: User says "commit", "ship this", "push it", or asks to create a git commit.
arguments:
     - name: scope
       description: Optional scope override (defaults to inferred from changed files)
allowed-tools: Bash(git *)
model: claude-sonnet-4-6
effort: medium
context: fork
agent: Default
paths: ["**/*"]
shell: true
hooks:
  PostToolUse:
    - matcher: Bash
      command: ./scripts/post-commit-lint.sh--
---
You are creating a git commit. Follow the conventional commits spec.
Steps:
1. Run `git status` and `git diff --staged`.
2. Pick a type: feat, fix, refactor, docs, test, chore.
3. Pick a scope from the changed files (or use $scope).
4. Write subject < 60 chars, imperative mood.
5. If breaking change, add `BREAKING CHANGE:` footer.
6. Read @conventional.md for the full spec.
7. Inline a status line:  !`git rev-parse --short HEAD` → previous SHA
8. Run `git commit -m "..."` (use heredoc for multi-line).
Print the commit hash when done. Working in: ${CLAUDE_SKILL_DIR}
```

# 11. Subagents and Agent Teams

Both features allow Claude Code to divide a large task into smaller pieces. The main difference is **how the agents communicate and coordinate their work**.

---

## A. What Is a Subagent?

A **subagent** is a specialized worker created inside your current Claude Code session.  
It receives a focused task, works in its own context window, and returns the result to the main agent.

```text
Main Claude session
        |
        | Assigns task
        v
     Subagent
        |
        | Returns result
        v
Main Claude session
```

For example, while developing a project, the main agent may delegate work to:

- A code-review subagent
- A security-analysis subagent
- A test-writing subagent
- A documentation subagent
- A codebase-exploration subagent

Each subagent can have its own:

- System prompt
- Context window
- Model
- Tool access
- Permission settings
- Hooks
- Skills

Because large search results, logs, and file contents remain inside the subagent's context, the main conversation stays cleaner.

---

## B. Simple Subagent Example

Suppose you ask:

```text
Analyze this Terraform project and identify security issues.
```

The main agent could delegate the work like this:

```text
Main agent
   |
   +-- Terraform security subagent
           |
           +-- Searches Terraform files
           +-- Checks IAM permissions
           +-- Checks open security groups
           +-- Checks unencrypted storage
           +-- Returns a summarized report
```

The subagent performs the detailed investigation, but only the useful findings are returned to the main conversation.

This is useful for DevOps work because codebase searches, Terraform reviews, Kubernetes manifest checks, and CI/CD log analysis can consume a large amount of context.

---

## C. Built-In Subagents

Claude Code includes built-in subagents such as:

### Explore

Used for fast, read-only codebase exploration.

```text
Find where the application reads AWS credentials.
```

The Explore agent can search and analyze files but cannot edit them.

### Plan

Used for researching the codebase and preparing an implementation plan.

```text
Plan how to migrate this project from Jenkins to GitHub Actions.
```

### General-Purpose

Used for more complex, multi-step tasks that may require broader tool access.

Claude Code can automatically choose a subagent when it determines that delegation is helpful.

---

## D. Custom Subagents

You can create your own reusable subagents as Markdown files containing YAML frontmatter.

### Project-Level Subagents

```text
your-project/
└── .claude/
    └── agents/
        ├── terraform-reviewer.md
        ├── kubernetes-reviewer.md
        └── documentation-writer.md
```

These are available only inside that project.

### Personal Subagents

```text
~/.claude/
└── agents/
    ├── security-reviewer.md
    └── powershell-reviewer.md
```

These are available across your projects.

---

## E. Custom Subagent Example

For a DevOps use case, create the following file:

```text
.claude/agents/terraform-reviewer.md
```

Add this content:

```yaml
---
name: terraform-reviewer
description: Reviews Terraform code for security, reliability, formatting, and AWS best practices. Use after Terraform files are created or modified.
tools: Read, Grep, Glob
model: sonnet
---

You are a Terraform and AWS infrastructure review specialist.

Review Terraform files for:

1. Security risks
2. Overly permissive IAM policies
3. Publicly accessible resources
4. Missing encryption
5. Missing tags
6. Hardcoded values
7. State management problems
8. Terraform formatting issues

Do not modify files.

For every issue, provide:

- File name
- Line number
- Problem
- Risk
- Recommended correction
```

Because only read-oriented tools are provided, this subagent can inspect the project but cannot modify files.

A clear `description` is important because Claude Code uses it to decide when it should delegate a task to that subagent.

---

## F. Invoking a Subagent

You can explicitly ask Claude Code to use a particular subagent:

```text
Use the terraform-reviewer subagent to review the files under infrastructure/.
```

You can also ask it to run multiple focused reviews:

```text
Use separate subagents to review:

1. Terraform security
2. Kubernetes manifests
3. GitHub Actions workflows

Return a consolidated report without modifying any files.
```

The basic workflow is:

```text
                    +-- Terraform reviewer
                    |
Main Claude agent --+-- Kubernetes reviewer
                    |
                    +-- GitHub Actions reviewer
                             |
                             v
                      Consolidated report
```

The workers perform focused tasks and report the results to the main agent.

---

## Agent Teams

## G. What Is an Agent Team?

An **agent team** is a collection of independent Claude Code sessions working together.

One session acts as the **team lead**. It creates tasks, assigns work, monitors progress, and combines the final results.

```text
                     Team lead
                  /      |       \
                 /       |        \
        Terraform     Kubernetes   CI/CD
        teammate      teammate     teammate
```

Unlike normal subagents, teammates can:

- Communicate directly with one another
- Share findings
- Challenge another teammate's conclusion
- Claim work from a shared task list
- Coordinate dependencies
- Receive messages directly from the user

Each teammate has its own independent context window.

Agent teams are best for complex work where collaboration between agents is necessary.

---

## H. Enabling Agent Teams

Agent teams are experimental and disabled by default.

### Linux or macOS

```bash
export CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS=1
```

### Windows PowerShell

```powershell
$env:CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS = "1"
```

### Using `settings.json`

### Using `settings.json`
 
Add the following configuration to your Claude Code `settings.json` file:
 
```json
{
"env": {
"CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS": "1"
}
}
```
 
Agent teams may have limitations involving:
 
- Session resumption
- Task coordination
- Teammate shutdown
- Token consumption
 
---
 
## I. Starting an Agent Team
 
After enabling the feature, describe the required team in natural language:
 
```text
Create an agent team to review this Kubernetes application.
 
Assign:
 
1. One teammate to review Kubernetes manifests.
2. One teammate to review container security.
3. One teammate to review CI/CD workflows.
4. One teammate to review monitoring and observability.
 
Ask the teammates to share important findings with each other.
The team lead should produce one consolidated report.
Do not modify files.
```
 
The team lead can then:
 
1. Create a shared task list.
2. Start the requested teammates.
3. Assign tasks or let teammates claim tasks.
4. Monitor their progress.
5. Resolve dependencies.
6. Combine the final results.
 
You can also select a teammate from the agent panel and communicate with that teammate directly.
 
---
 
## J. Subagents vs Agent Teams
 
| Feature | Subagents | Agent Teams |
|---|---|---|
| Structure | Worker inside one session | Multiple independent sessions |
| Context | Separate context window | Each teammate has an independent context |
| Communication | Returns results to the main agent | Teammates communicate directly |
| Coordination | Main agent manages the work | Shared task list and team coordination |
| Token usage | Usually lower | Usually higher |
| Complexity | Simpler | More complex |
| Best for | Focused and isolated tasks | Large collaborative tasks |
| Status | Regular Claude Code feature | Experimental |
| Example | Review one Terraform module | Review an entire cloud platform |
 
> **Simple rule:** Use **subagents** when workers only need to return their results. Use an **agent team** when workers need to communicate and coordinate with each other.
 
---

# 12. MCP (Model Context Protocol)
- This is a protocol, not any tool
```
You -> [LLM -> Tool]
Suppose the tool is Uber Cab Booking tool. So How it is supposed to recognize the Uber tool as it is external.
MCP!!!!

You
  ↓
Claude
  ↓
MCP Client
  ↓
MCP Server
  ↓
GitHub / Jira / AWS / Files / Database / Kubernetes
```
- Model Context Protocol = standardized way for external systems to expose tools to Claude.
GitHub, Slack, Postgres, Linear, Notion, Puppeteer, your internal API — they all become tools Claude can
call
- **Example: Add Github MCP server**
  1. Go to google and type "Github MCP server"
     <img width="922" height="661" alt="image" src="https://github.com/user-attachments/assets/b36332d8-3bc4-4f78-b40b-a11342aa7f18" />


