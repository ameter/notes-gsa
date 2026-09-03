ok, also, just to quickly revisit the question about the other place this could be relevant... you said "Per your answer, AamvaPlugin#user_can_pass_after_state_id_check? (aamva_plugin.rb:155-175) is left alone — it's unreachable in production (all four callers pass doc_auth_flow: true, which short-circuits at :144)."

I just want to confirm, are those "doc_auth_flow: true" calls you were referring to hardcoded or are they based on a config value that could be changed without a code change?


- When disabled, adjudication falls through to the existing `fail_resolution_without_state_id_coverage` reason, so no new analytics field or attempts-API enum value is added. Distinguishing "flag off" from "genuinely not covered" in analytics is a possible follow-on.



```
vendors

mock, instant_verify, instant_verify_ddp, socure_kyc
```

  idv_resolution_default_vendor: 'instant_verify'
  idv_resolution_vendor_instant_verify_ddp_percent: 50
  idv_resolution_vendor_instant_verify_percent: 0
  idv_resolution_vendor_socure_kyc_percent: 50
  idv_resolution_vendor_switching_enabled: true


analytics for aamva rescue:
- name: idv_doc_auth_verify_proofing_results
- field: proofing_results.context.resolution_adjudication_reason: state_id_covers_failed_resolution
- field: proofing_results.context.stages.resolution.vendor_name: <vendor name from above>


name    = idv_doc_auth_verify_proofing_results
field   = proofing_results.context.resolution_adjudication_reason == 'state_id_covers_failed_resolution'
segment = proofing_results.context.stages.resolution.vendor_name



potential follow-on tickets
- dead code removal (behind hardcoded trues at 4 call-sites) in aamva plugin.
- idv_resolution_default_vendor's enum omits instant_verify_ddp, so DDP can't be set as the default vendor even though create_proofer supports it. Worth its own ticket.



For adding Socure aamva-rescue:

One caveat that only applies once Socure sets the rescue fields: attributes_requiring_additional_verification will start carrying first_name/last_name, which are not in the attempts-API enum (IdvVerificationSubmitted.yml:59-64: address, dob, dead, ssn, state_id_number). That's a partner-visible schema gap the Socure ticket will need to close — worth noting on the ticket now.