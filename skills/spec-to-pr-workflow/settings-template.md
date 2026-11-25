# Delivery workflow template

Add this section to the project's CLAUDE.md. Both skills read it; without it,
Claude asks for these once.

```markdown
## Delivery workflow

- **Base branch:** <base branch>. <production branch and how it is updated>.
- **Branch names:** <branch naming rule>.
- **Risk:** <areas that are always High here, e.g. payments, auth; anything the project rates differently>.
- **Pre-push commands:** <style, lint, static analysis, gates, the tests for the change>.
- **Wrong local runtime:** <how to run the commands in Docker or another runtime>.
- **Run locally:** <how to start the API and each client; test accounts and where their passwords live>.
- **Test environment:** <URL; how it is deployed from the base branch>.
- **External services in tests:** <how mail, payments, auth and push are faked>.
- **Tracker:** <board>; columns: <in progress> → <in review> → <merged / deployed to test>. Columns after that belong to <who>.
- **Specs and plans:** <folder, or the shared place where specs live>.
- **Merging:** <who merges; merge commits or squash>.
```
