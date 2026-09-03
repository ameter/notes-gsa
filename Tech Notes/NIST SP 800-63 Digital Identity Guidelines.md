- Identity Assurance Level (IAL)

- How confident you are that the person is who they claim to be.

- This is about identity proofing/enrollment.

- Examples:
	- IAL1: Little/no identity proofing required
	- IAL2: Remote or in-person proofing with validated evidence
	- IAL3: Stronger proofing with higher confidence and stricter controls
- Authenticator Assurance Level (AAL)

- How strong the login/authentication process is.

- Examples:
	- AAL1: Basic authentication (e.g. password)
	- AAL2: MFA required (e.g. password + passkey/TOTP)
	- AAL3: Strong phishing-resistant cryptographic authentication with stricter requirements
- Federation Assurance Level (FAL)

- How strong and secure federated identity assertions/tokens are between systems (IdP ↔ RP).

- Examples:
	- FAL1: Basic federation
	- FAL2: Signed/encrypted assertions with stronger protections
	- FAL3: Assertion bound to a specific authenticator/key (“holder-of-key” style protections)

Simple way to think about it:

- IAL = “Who are you?”
- AAL = “How did you prove it’s you right now?”
- FAL = “How securely is that identity/authentication information shared between systems?”



A CSP is a Credential Service Provider.

Very roughly:

- CSP = “the thing responsible for your credential and authentication”
- IdP = “the thing telling another system you authenticated”

Example:

You log into a government app using <u>Login.gov</u>.

Login.gov:

- verifies your identity
- stores/manages your MFA/passkey
- authenticates you

That’s the CSP role.

Then Login.gov sends a signed assertion/token to the government app saying:

- “Chris authenticated successfully at AAL2”

That’s the IdP role.

The government app is the RP (Relying Party).

So:

User → CSP/IdP → RP

Where:

- CSP focuses on credential lifecycle and authentication
- IdP focuses on federation/assertions to other systems
⸻


**SP 800-63-4: Digital Identity Guidelines**

Top-level framework for digital identity risk management. It covers identity proofing, authentication, federation, authenticators, assertions, management processes, privacy, fraud, usability, and continuous evaluation. It supersedes SP 800-63-3.

Key points:

- Treats digital identity as a risk-management process, not just a technical checklist.
- Splits assurance into IAL, AAL, and FAL, then requires organizations to select, tailor, document, and continuously evaluate them.
- Requires a “Digital Identity Acceptance Statement” documenting final assurance levels, compensating controls, supplemental controls, privacy/customer-experience considerations, and risk rationale.
- Adds stronger emphasis on fraud, privacy, customer experience, redress, AI/ML use, and performance metrics.
- Major Rev. 4 themes include subscriber-controlled wallets, syncable authenticators/passkeys, forged-media/deepfake defenses, continuous evaluation metrics, and cross-functional risk management.

**SP 800-63A-4: Identity Proofing and Enrollment**

Covers how a CSP establishes confidence that an applicant is tied to a real-life identity, then enrolls them into a subscriber account. It defines requirements for IAL1, IAL2, and IAL3.

Key points:

- Reframes proofing around resolution, validation, verification, then enrollment.
- Separates global proofing requirements from IAL-specific requirements.
- Introduces “core attributes” and decouples attribute collection from evidence collection.
- Expands acceptable validation sources to include credible sources, not only authoritative ones.
- Adds fraud-management requirements, digital-injection prevention, and forged-media detection.
- Adds exception handling, trusted referees, applicant references, and requirements for interacting with minors.
- Updates IAL1 for lower-risk use cases and adds non-biometric verification options at IAL1/IAL2.

**SP 800-63B-4: Authentication and Authenticator Management**

Covers authenticating a claimant as a subscriber and managing authenticators across AAL1, AAL2, and AAL3.

Key points:

- AAL1 allows single-factor authentication; AAL2 requires MFA or equivalent two-factor authentication; AAL3 requires stronger phishing-resistant/non-exportable cryptographic authentication.
- Federal systems exposing personal information online must use at least AAL2.
- “Memorized secrets” are now simply called passwords.
- Password requirements are updated, including stronger minimums for single-factor passwords and continued focus on blocklists, usability, and secure verifier storage.
- Adds explicit support/requirements for syncable authenticators such as passkeys.
- Adds wallet-related authentication requirements.
- Clarifies phishing-resistant authenticators.
- Updates biometric requirements, requires PAD for facial recognition, and prohibits voice-based biometric comparison.

**SP 800-63C-4: Federation and Assertions**

Covers federated identity, assertions, IdPs, RPs, trust agreements, and FAL1/FAL2/FAL3. Federation lets CSPs/IdPs provide authentication and subscriber attributes to separately administered RPs.

Key points:

- FALs now focus on protections for federation transactions: audience restriction, replay protection, assertion-injection protection, trust agreement establishment, identifier/key establishment, and presentation method.
- FAL1 is basic federation; FAL2 requires stronger protections including assertion-injection protection; FAL3 adds holder-of-key or bound-authenticator protections.
- Trust agreements and registration/discovery are now explicit steps.
- Encryption is no longer tied directly to FAL; it is required when personal information passes through an untrusted party.
- Adds explicit communication of IAL/AAL/FAL.
- Adds RP subscriber account models.
- Adds subscriber-controlled wallet federation requirements.
- Adds redress requirements for IdPs and RPs.
⸻
SP 800-63A-4 is about **identity proofing**: establishing that an online applicant corresponds to a real-life person. NIST frames proofing as: **resolution → validation → verification → enrollment**.

**Core concepts**

- **Resolution:** collect evidence/attributes and determine the applicant maps to a unique real-life identity.
- **Validation:** confirm evidence is genuine, authentic, accurate, and valid.
- **Verification:** confirm the applicant is the rightful owner of that evidence.
- **Enrollment:** create the subscriber account and bind authenticators.

**IAL1: basic proofing**

IAL1 provides **some assurance** that the claimed identity exists and the applicant is associated with it. It is meant to reduce scalable fraud, synthetic identities, and use of compromised personal info.

Requirements:

- Can be remote or on-site, attended or unattended.
- Collect **one** piece of fair, strong, or superior evidence.
- Collect all core attributes, including at least one government identifier.
- Validate core attributes against authoritative or credible sources.
- Verify applicant ownership of **one** piece of evidence.
- Biometric matching is optional.

Think: “Is this probably a real person tied to this claimed identity?”

**IAL2: stronger remote/in-person proofing**

IAL2 adds more evidence, stronger validation, and stronger verification. It is aimed at scaled and targeted attacks, basic evidence falsification, stolen evidence, and social engineering.

Requirements:

- Can still be remote or on-site, attended or unattended.
- Collect either:
	- 1 fair + 1 strong evidence, or
	- 2 strong evidence, or
	- 1 superior evidence.
- Collect all core attributes, including at least one government identifier.
- Validate all evidence.
- Superior evidence requires cryptographic validation of contents and issuer/trust chain.
- Verify applicant ownership of the presented evidence.
- Supports multiple verification paths:
	- non-biometric,
	- biometric,
	- digital evidence.
- CSPs should support more than one IAL2 verification path and must record which path was used.

Think: “Do we have enough evidence and process strength to resist common fraud and impersonation?”

**IAL3: highest assurance proofing**

IAL3 adds direct proofing-agent involvement, biometric collection, and stricter in-person-style controls. It is aimed at more sophisticated attacks, advanced evidence falsification, theft, repudiation, and advanced social engineering.

Requirements:

- Must be **on-site attended**.
- Proofing agent may be physically co-located or remote through a CSP-controlled kiosk/device.
- Evidence collection is the same minimum as IAL2:
	- 1 fair + 1 strong, or
	- 2 strong, or
	- 1 superior.
- Collect all core attributes plus a biometric sample.
- Verify ownership of all presented evidence.
- Initial authenticator must be distributed or enrolled during an on-site attended interaction.
- Kiosk-based IAL3 requires CSP-controlled devices, monitored sessions, integrated scanners/sensors, tamper protections, and FISMA-moderate-comparable controls.

Think: “High confidence, in-person-grade process, with biometric binding and fraud-resistant controls.”


| Area | IAL1 | IAL2 | IAL3 |
| -- | -- | -- | -- |
| Main purpose | Basic real-world identity confidence | Stronger fraud/impersonation resistance | Highest assurance, in-person-grade proofing |
| Proofing type | Remote/on-site, attended/unattended | Same | On-site attended only |
| Evidence | 1 fair/strong/superior | 1 fair + 1 strong, or 2 strong, or 1 superior | Same as IAL2 |
| Attributes | Core attributes + gov identifier | Same | Same + biometric sample |
| Verification | Ownership of one evidence item | Ownership of presented evidence | Ownership of all evidence |
| Biometrics | Optional | Optional depending path | Required collection/retention |
| Authenticator binding | After enrollment | After enrollment | During on-site attended interaction |