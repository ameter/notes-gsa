
- Right now, LN reports failed attributes from resolution, but Socure does not
- Socure reports verified attributes, which is how we derived failed for Socure?
- LN does NOT report verified attributes, it only reports failed.

LN returns DriversLicenseVerification with ItemReason: {"Code": "name_not_verified_fail"} — so LN does return a name-specific failure signal, in the reason code.

But it's not a discrete name check. It's a DL-verification check whose reason happens to be a name mismatch. LN has no standalone "first name" or "last name" item the way Socure has firstName/surName fields.

___

Out of scope - 131
- Enabling socure_kyc in the flag — separate config-only change
- Names support, which needs the attempts-API enum extended — separate ticket
- Pre-existing: :unknown isn't in the attempts-API enum (IdvVerificationSubmitted.yml:59-64) yet LN already emits it to partners — 991 events confirmed in prod. Worth its own ticket; this change propagates a known gap rather than creating one.


___

>ok, so you are recommending we map socure failed attributes to the same ones that we output from the LN mapper, and any failed attributes we don't currently output from the LN attribute mapper, we map from socure as "unknown", which can't break anything because we already output a bunch of those from the LN attribute mapper currently anyway. Correct??

The mapping direction differs. LN maps check names → attributes. Socure has no check names; it has attribute-shaped fields already. So there's nothing to translate for address, dob, ssn — those pass through directly.

The only mapping needed is for what Socure reports that LN's output vocabulary doesn't include: first_name and last_name → :unknown.

Rest of your statement is right:

Socure's reported values end up being a subset of LN's output vocabulary (address, dob, ssn, unknown)
:unknown breaks nothing new, since LN already emits it (991 events confirmed)
:unknown can never match aamva_verified_attributes, so it correctly blocks a rescue

___

Yes. first_name and last_name → :unknown; address, dob, ssn pass through unchanged.

Nothing "changes" for Socure though — it currently reports [] for everything, so all five are new output.

4 of the 6 that Socure returns already exactly match what we map LN failures to




```
# Counts of prod events where the resolution stage reported unknown among its failed attributes — 991 total across 10 distinct combinations.
SOURCE "arn:aws:logs:us-west-2:555546682965:log-group:prod_/srv/idp/shared/log/events.log" START=-3h END=0s |
filter name = 'IdV: doc auth verify proofing results'
| parse @message '"attributes_requiring_additional_verification":[*]' as attrs
| filter attrs like /unknown/
| stats count(*) as n by attrs
```





Measured 2026-09-02 over a 30 day window.

| Metric          | Value   |
| --------------- | ------- |
| Socure failures | 167,002 |
| Would rescue    | 36,454  |

- 21.8% rescue rate
- 1,215 users / day


## What's required (~15 lines)

`KycResponse`already computes everything. Add two methods and pass them into the`Result`:

- `attributes_requiring_additional_verification`=`REQUIRED_ATTRIBUTES - verified_attributes`(`kyc_response.rb:17-23`,`:37-43`)
- `failed_result_can_pass_with_additional_verification?`
- Wire both into`build_result_from_response`(`kyc_proofer.rb:26-37`)

Then add`"socure_kyc"`to`idv_aamva_get_to_yes_enabled_vendors`. No gate change needed, that's what 117's per-vendor list bought you.`idv_socure_kyc_results`already declares both fields (`analytics_events.rb:6150-6151`), currently always logging defaults.

## Gotchas

1. **No reason-code gate equivalent.**LN uses`transaction_reason_code`+`ProductStatus == 'fail'`to distinguish "failed on attributes" from "failed for other reasons." Socure has nothing comparable, so you must define the rule explicitly.
2. **Autofail codes.**`successful?`is`all_required_attributes_verified? && !has_autofail_reason_codes?`(`:33-35`). A user can fail on codes alone with zero failed attributes, so`can_pass`must exclude that case deliberately, not incidentally.
3. **`ssn`is unrescuable.**AAMVA doesn't verify SSN (`aamva/proofer.rb:24-29`), and`ssn`is in Socure's`REQUIRED_ATTRIBUTES`. Any SSN failure is permanently ineligible. Probably a large share of Socure failures.
4. **Names become newly rescuable, and that's partner-visible.**Socure fails`first_name`/`last_name`; AAMVA verifies both. Set subtraction at`result_adjudicator.rb:165`would rescue on name, which has never happened in prod. Those values are**not**in the attempts-API enum (`IdvVerificationSubmitted.yml:59-64`:`address, dob, dead, ssn, state_id_number`), so allowing it requires a schema change. Either extend the enum or exclude names.
5. **No`state_id_number`or`dead`signal**from Socure, unlike LN.
6. **Blast radius is 50% of prod.**

## Recommendation

The ticket says "Evaluate," and the data is already logged, so**do the analysis first, no code**: query`idv_socure_kyc_results`for failed results where`can_pass_with_additional_verification`would be true and the failed attributes are a subset of`state_id_verified_attributes`. That number tells you whether the 15 lines are worth shipping, and how much of the win depends on names.

Want me to draft that query plan, or plan the implementation?