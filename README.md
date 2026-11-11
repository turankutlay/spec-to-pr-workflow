# spec-to-pr-workflow

A Claude Code skill: the review gates between an idea and a merged pull
request, with four owner decision points per PR.

## Install on a computer

Clone this repository into the personal skills folder:

```bash
git clone https://github.com/turankutlay/spec-to-pr-workflow.git ~/.claude/skills/spec-to-pr-workflow
```

Update later with `git -C ~/.claude/skills/spec-to-pr-workflow pull`.

The skill needs the superpowers plugin and the built-in `code-review` skill.

## Use in a project

Add a `## Delivery workflow` section to the project's CLAUDE.md with: base
branch, branch naming, pre-push commands, how to run them when the local
runtime is the wrong version, tracker board and list names, and where specs
and plans are saved. Without it, Claude asks for these once.
