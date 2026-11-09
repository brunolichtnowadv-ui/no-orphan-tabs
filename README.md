# No Orphan Tabs

**A skill rule that stops Claude in Chrome from leaving "orphan" tab groups behind when a conversation ends.**

Related issue: [anthropics/claude-code#108193](https://github.com/anthropics/claude-code/issues/108193)

## The problem

When Claude drives Chrome through the Claude in Chrome extension, each conversation gets its own tab group, and only that conversation can see and close it. If the conversation ends without closing its tabs, the group is orphaned: no later conversation can close it, and the user has to close each leftover group by hand.

## The workaround

Make tab cleanup part of every browser task, in this order:

1. **One tab per site during the task.** Reuse the same tab for each system instead of opening a new one per step. Close helper tabs as soon as they have done their job.
2. **Close before the final reply.** When the task is done, close every tab in the conversation's group with `tabs_close_mcp`, then call `tabs_context_mcp` to confirm the group is gone. Only then write the final reply. Cleanup is the last tool call of the task, never left for later, because once the conversation ends nobody else can close the group.
3. **Only what the task opened.** Never close tabs the user already had open, or tabs that belong to another conversation still running. When in doubt, leave the tab open.
4. **A task waiting on the user is not finished.** If the reply ends asking the user to do something (approve, sign, confirm), the group may stay open until the user's next message. On that message, the first thing is to check the result, close the group if the task is done, and only then answer.
5. **Subagents close their own tabs.** The main conversation checks that none were left behind.
6. **Orphans from earlier conversations.** Claude cannot close them. Tell the user in one line to right-click the group name in the tab strip and close the group.

## Install

Copy [`skill/SKILL.md`](skill/SKILL.md) into a folder named `no-orphan-tabs` and add it as a skill (Claude Code: `.claude/skills/` or `~/.claude/skills/`; Claude app: custom skill).

## Limitations

This is a workaround. An interrupted or failed turn can still leave an orphan. The real fix belongs in the extension: dissolve a conversation's group when it ends, or let a new conversation list and close groups left by earlier ones.

## License

[MIT](LICENSE)
