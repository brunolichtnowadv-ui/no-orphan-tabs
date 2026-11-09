---
name: no-orphan-tabs
description: Use in every task that opens tabs with Claude in Chrome, and at the end of that task, to reuse one tab per site and close the conversation's tab group before the final reply, so no orphaned groups are left behind.
---

# No orphan tabs

1. **During the task:** one tab per site, reused across steps. Close helper tabs as soon as they are no longer needed.
2. **At the end:** close every tab in this conversation's group with `tabs_close_mcp`, confirm with `tabs_context_mcp` that the group is gone, and only then write the final reply. This is always the last tool call of the task.
3. **Only what the task opened:** never close tabs the user already had, or tabs of another conversation in progress. When in doubt, leave it open.
4. **Waiting on the user:** if the reply ends asking the user to act, the group may stay open until the next message. On that message, first check the result, close the group if the task is done, then answer.
5. **Subagents:** close their own tabs; the main conversation checks none were left.
6. **Orphans from earlier conversations:** cannot be closed from here. Tell the user in one line to right-click the group name in the tab strip and close the group.
