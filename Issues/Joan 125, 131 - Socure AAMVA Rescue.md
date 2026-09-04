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