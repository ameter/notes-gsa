One-liner: Concerns = mixed-in shared behavior; FlowPolicy = the step graph + per-step precondition/undo rules; controllers delegate "may I?" and "reset downstream" to the policy via concern helpers.




```
VerifyInfoController#update
 → VerifyInfoConcern#shared_update
   → Idv::Agent#proof_resolution        (encrypt PII, create result slot)
     → ResolutionProofingJob.perform_(later|now)   ← ASYNC BOUNDARY (config-gated)
       → #make_vendor_proofing_requests
         → ProgressiveProofer#proof      (plugin chain → ResultAdjudicator)
       → DocumentCaptureSession#store_proofing_result(result_id)
 ... later ...
VerifyInfoController#show → load_async_state(result_id)   ← reads result back
```