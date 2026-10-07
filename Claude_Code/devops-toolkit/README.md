# DevOps Toolkit Sample Plugin

An educational Claude Code plugin containing one manually invoked CI/CD review skill.
No hooks, MCP servers, cloud credentials, or deployment scripts are included.

## Structure

```text
devops-toolkit/
  .claude-plugin/plugin.json
  skills/review-pipeline/SKILL.md
  README.md
```

## Try it

1. Extract the archive.
2. Open a terminal in the repository whose pipeline you want to review.
3. Start Claude Code with the extracted plugin's path:

```bash
claude --plugin-dir /absolute/path/to/devops-toolkit
```

On Windows, replace the path with your actual extracted directory, for example:

```powershell
claude --plugin-dir "C:\Plugins\devops-toolkit"
```

4. Inside Claude Code, invoke:

```text
/devops-toolkit:review-pipeline .github/workflows/ci.yml
```

Or let the skill locate a pipeline:

```text
/devops-toolkit:review-pipeline
```

The supplied path must exist in your repository. No sample pipeline is bundled.
This local session loading does not require a marketplace.

## Design

- plugin.json names the plugin and sets its version.
- SKILL.md defines the review instructions.
- disable-model-invocation requires manual invocation.
- context: fork with agent: Explore uses an isolated exploration subagent.
- allowed-tools pre-approves reading tools; it is not a tool allowlist.
- disallowed-tools removes the listed execution/editing tools while the skill is active.
- The body additionally instructs the agent to use only Read, Glob, and Grep.

These instructions are not a security sandbox. Keep your organization's permission
controls enabled. Review outputs before applying suggested changes.

## Validation

Archive integrity, manifest JSON, and skill frontmatter were checked during creation.
The plugin has not been loaded or executed in Claude Code in this environment.

## Official references

- https://code.claude.com/docs/en/plugins/create
- https://code.claude.com/docs/en/skills
