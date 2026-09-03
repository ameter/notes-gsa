**Client ID:**
api-key-f43ba6d6-98c1-4a5b-9e1a-82cba2f37acc

**Client Secret:**
OGvAF3YQsUozY1P5YVZXzmMyUNQrFsEx

**API Key (paste this):**
api-key-f43ba6d6-98c1-4a5b-9e1a-82cba2f37acc:OGvAF3YQsUozY1P5YVZXzmMyUNQrFsEx



Create context history folder in sandbox, and write context out to it.

Christopher Weibel - F20AA

Swagger API
<a href="https://api.gsa.usai.gov/docs#/LLM%20API/models_api_v1_models_get" rel="noopener" class="external-link" target="_blank"><u>https://api.gsa.usai.gov/docs#/LLM%20API/models_api_v1_models_get</u></a>

William Zujkowski

If you use a different GitLab instance, update AGENTS_SBX_ADDENDUM.md and docs/SBX_PATTERNS.md to reference your host instead of workshop.cloud.gov.

sbx policy allow network -g plugins.cloudfoundry.org
sbx policy allow network -g "*.fr.cloud.gov"
sbx policy allow network -g "*.app.cloud.gov"





```
sbx policy set-default balanced  
sbx policy allow network -g "api.gsa.usai.gov"

sbx secret set -g USAI_API_KEY


sbx exec -it -e USAI_API_KEY="$USAI_API_KEY" -w $(pwd) quickstart opencode

sbx ls
sbx secret ls

```