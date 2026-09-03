https://github.com/18F/identity-idp/pull/13166#pullrequestreview-4404382041


- The order change (phone_check_fail now after id_fail/passport_fail) is probably fine - ID/passport failures are more fundamental
- The test coverage is adequate
- The threading of is_proofing_agent through the chain is clean