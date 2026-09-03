- populate in the Socure KYC response:
	- attributes_requiring_additional_verification
	- can_pass_with_additional_verification

Socure reports failures differently than LexisNexis
	so we have to define the eligibility rule ourselves

two of its failure attributes mean the actual benefit is an open question.
- SSN, which AAMVA never verifies
- first/last name, which it does but we've never rescued on

The wrinkle is that Socure reports failures differently than LexisNexis, so we have to define the eligibility rule ourselves rather than copy it, and two of its failure attributes (SSN, which AAMVA never verifies, and first/last name, which it does but we've never rescued on) mean the actual benefit is an open question. So the plan is a logging-only PR first to measure how many users this would actually rescue before enabling it.


_____


1. Review it as the real thing, not a logging tweak. The eligibility rule (what replaces LexisNexis's reason-code gate) is the substance of the PR and deserves real scrutiny, since it's what determines who gets rescued once enabled.
2. The names/attempts-API question becomes live at that PR, not later — if first_name/last_name land in attributes_requiring_additional_verification, they're published to partners immediately via idv-verification-submitted, flag or no flag. Excluding names keeps the PR trivial but costs you the ability to measure their upside.

So framing for standup: one PR, full implementation, deployed off; measure; then a config-only change to enable.


______



```
idv_socure_kyc_auto_failure_reason_codes: '["R995", "R947"]'
```