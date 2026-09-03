https://docs.docker.com/ai/sandboxes/

- Run AI coding agents in microVM sandboxes.
- Own kernel, etc.  Runs on the hypervisor.


brew install docker/tap/sbx
sbx login

# 1. Set network policy (first-time only)
sbx policy set-default balanced

# 2. Allow USAi endpoint
sbx policy allow network -g "api.gsa.usai.gov"

# 3. Store your USAi API key securely (USAi is a custom endpoint, not built-in)
sbx secret set-custom -g --host api.gsa.usai.gov --env USAI_API_KEY --value "$USAI_API_KEY"

# 4. Store GitHub token (for code access)
gh auth token | sbx secret set -g github

# 5. Start a sandbox in your project
cd /path/to/your/project
sbx run opencode .


cd ~/my-project
sbx run \[codex | claude | opencode\]


Command Reference

sbx ls
sbx rm \[name\]

_________


```
 sbx create --name blah opencode .

 sbx exec -it blah bash
```