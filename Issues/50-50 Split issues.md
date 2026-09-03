The real miss: regardless of which PR owns it, I never flagged the form param name as a 50/50 surface at all during planning. I reasoned thoroughly about session/redis/profile persistence and even wrote a doc on deprecated attributes — but I didn't extend the same "who renders vs. who processes" lens to form submissions. 

That's the gap: I applied the 50/50 discipline to persisted data but not to in-flight request/response shape, which the handbook actually calls out separately (the "JavaScript/API request shape" case is the analogous one).


Surface	Status
Redis session/PII	✅ handled
DB profile PII	✅ handled
Job perform args (explicit)	✅ not an arg
Job encrypted applicant_pii (in-flight)	⚠️ verify producers populate new key
Routes	✅ N/A
Form/request params (State ID)	⚠️ known gap (fix in PR-2)
Form params (Address)	✅ not exposed
JS/API shape	✅ N/A
Feature flags	✅ N/A