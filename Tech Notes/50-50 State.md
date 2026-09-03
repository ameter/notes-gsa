Compatibility-sensitive work often needs staged PRs/merges:

1. PR 1: add schema/field, old code still works
2. deploy
3. PR 2: start writing new field
4. deploy
5. PR 3: start reading new field
6. deploy
7. later: remove old field/behavior

Their 50/50 doc gives this kind of staged guidance: add/write first, read later; remove reads, then writes, then field.

Examples of problems they explicitly guard against:

- new code writing DB fields old code can’t handle
- renamed Redis/session fields
- job queue argument mismatches
- routes existing on only one version
- frontend expecting API response shapes only new servers provide