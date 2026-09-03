```
opencode-replace-context idp3 ~/opencode-context-backup2/opencode/


qsbx create --name idp3 opencode . /Users/christopherrameter/Developer/ameter-docs




opencode session list
opencode export <sessionID> > blah.json
opencode import /path/to/session.json
opencode --continue or opencode --session <sessionID>

```


**List all sessions:** Run opencode session list from your terminal to see a summary of all past sessions and their IDs.
**Export a session:** Use opencode export <sessionID> to export a full conversation history and metadata as a readable JSON file.
**Continue a session:** You can resume an earlier session in the terminal by using opencode --continue or opencode --session <sessionID>. \[<a href="https://www.reddit.com/r/opencodeCLI/comments/1t5imcb/does_opencode_have_a_global_search_for_all_its/" rel="noopener" class="external-link" target="_blank"><u>1</u></a>, <a href="https://opencode.ai/docs/cli/" rel="noopener" class="external-link" target="_blank"><u>2</u></a>\]


Project Directory Bug (⚠️ Alert): Keep in mind that a known OpenCode bug forces imported sessions into a "global" project state, occasionally causing them to hidden from your local workspace sidebar or opencode session list. If you import a session inside a specific project repository and it doesn't appear, you can fix it by manually updating your local opencode.db SQLite file:

-- Find your project ID
SELECT id, worktree FROM project;

-- Update the hidden session to point to your project
UPDATE session SET project_id = '<your_project_id>', directory = '<your_project_path>' WHERE id = 'ses_abc123';



___________________



```
acq create --name idp opencode ~/Developer/identity-idp ~/Developer/ameter-docs
```


____________________




```
Current good commit: fbd57be188974510ebf30d856aa86f1e55d24218
```