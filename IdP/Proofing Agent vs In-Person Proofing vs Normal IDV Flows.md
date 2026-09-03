The user has already visited the partner location, shown their documents in person, and the partner collected their PII. Then the partner calls Login.gov's API with that info, which triggers this entire flow.

1. ProofingAgentController#proof_user - API endpoint receives request from partner (NOT USPS) with user's PII
2. ProofingAgent::ProofUser - Encrypts args, enqueues the job
3. ProofingAgentJob - Orchestrates verification:
	- Calls AAMVA (state ID check) if state ID provided
	- Calls MRZ (passport check) if passport provided
	- Calls ResolutionProofingJob for address/phone verification
1. ResolutionProofingJob - Runs the ProgressiveProofer
1. ProgressiveProofer#proof - Calls multiple plugins:
	- ThreatMetrix (device profiling)
	- Residential address plugin
	- State ID address plugin
	- PhonePlugin ← phone check skipped here if precheck disabled
1. ProofingAgent::ProofingResult - Combines all results, determines success/failure reason
1. ProofingAgentWebhookJob - Notifies partner of result via webhook



Normal IDV Flow (self-service online)
1. User uploads documents (driver's license, passport) via Login.gov website
2. Document auth checks the images
3. ResolutionProofingJob → ProgressiveProofer runs:
	- ThreatMetrix (device profiling)
	- Address resolution
	- Phone check (percentage rollout - may skip)
1. User manually verifies phone via SMS OTP
1. User completes proofing, can access Service Provider

Proofing Agent Flow (in-person)
1. User visits partner location (NOT USPS), shows documents in person
2. Partner collects PII, calls Login.gov API (proof_user)
3. ProofingAgentJob runs:
	- AAMVA (state ID) / MRZ (passport) checks
	- ResolutionProofingJob → ProgressiveProofer (same as above)
1. Webhook notifies partner of result
1. User completes proofing, can access Service Provider
Key difference: In normal IDV, user does phone verification themselves (step 4). In Proofing Agent flow, there's no manual phone OTP step - the phone check in PhonePlugin is the *only* phone verification, so it must always run.



## Full In-Person Proofing User Journey - USPS

Before Post Office
1. User starts on Login.gov website, chooses "verify in person"
2. Enters their info (state ID, SSN, address) online
3. Login.gov calls USPS API to create enrollment
4. User gets a barcode and "Ready to Verify" email
5. Enrollment status: pending
At Post Office
1. User brings barcode + ID to Post Office
2. USPS clerk scans barcode, checks documents
3. User leaves - no immediate result
After Post Office
1. GetUspsProofingResultsJob polls USPS API periodically (background job)
2. When USPS reports pass/fail, user gets email
3. User returns to Login.gov website to see result

Important clarification: The ProofingAgentJob we've been looking at is actually for a *different* flow - it's for real-time API-based proofing partners, not the USPS barcode flow. The USPS flow uses GetUspsProofingResultsJob instead.
So the Jira ticket (LG-17598) is about a different type of in-person proofing - one where a partner calls Login.gov's API in real-time to submit verification results, not the USPS barcode/polling flow.


## Full Proofing Agent User Journey (Real-time Partner API)

Before Partner Visit
1. User creates a Login.gov account (email only, not fully verified)
2. User wants to access a Service Provider that requires identity verification
At Partner Location
1. User visits an authorized partner location (not USPS barcode flow - could be another type of kiosk/partner)
2. Partner collects user's documents (license, passport) and PII in person
3. Partner's system calls Login.gov API:
	- POST /api/proofing_agent/search_user - confirms account exists
	- POST /api/proofing_agent/proof_user - submits PII for verification
1. ProofingAgentJob runs in real-time:
	- AAMVA (state ID check) or MRZ (passport check)
	- Resolution proofing (address, phone)
1. Partner polls POST /api/proofing_agent/result OR receives webhook with pass/fail
After Partner Visit
1. User's profile is now verified in Login.gov
2. User can log into Login.gov and access Service Providers

Key difference from USPS flow:
- USPS = barcode + background polling job (GetUspsProofingResultsJob)
- Proofing Agent = real-time API calls (ProofingAgentJob)



Proofing Agent flow:
1. User creates a Login.gov account on the website (email + password + MFA setup)
2. User goes to partner location
3. Partner submits PII via API
So the user does touch the Login.gov website to create an account, but they don't go through the IDV flow online. They skip straight from "account created" to "visit partner for in-person verification."

In contrast, USPS IPP:
1. User creates Login.gov account
2. User starts IDV online (enters PII, does phone OTP verification)
3. User gets barcode, goes to Post Office
4. USPS verifies physical documents

The key difference: In PA, the user skips the online IDV steps (including phone OTP). In IPP, they do the online IDV steps first, then go in-person for document verification.


## Normal IDV Flow (Online Self-Service)

1. User creates Login.gov account (email + password + MFA setup)
2. User starts IDV on Login.gov website to access a Service Provider
3. Document upload - user photographs their driver's license/ID
4. Document verification - Login.gov checks the document images
5. PII entry - user enters/confirms SSN, address
6. ProgressiveProofer runs:
	- ThreatMetrix (device profiling)
	- Address resolution (LexisNexis/Socure)
	- PhonePlugin (data check - percentage rollout)
1. Phone OTP verification - user enters phone number, receives SMS code, proves possession
1. Profile activated - user can access Service Provider
Key point: Step 7 (phone OTP) is the primary phone verification. Step 6's PhonePlugin is an *additional* data check being gradually rolled out - it doesn't replace OTP.
For Proofing Agent: Steps 3-7 don't happen online. The partner collects everything in-person and submits via API. No OTP step exists, so PhonePlugin becomes the only phone verification.