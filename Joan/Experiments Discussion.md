celebrate phone step wins

talk about doing other things at resolution step like phone check (address, name, etc)


on phone ownership issue
- fix that failure issue
- rolling out part one of fix today
- rolling out part two of fix on tuesday

let's explore other areas where we can address dead ends because that wrong phone type number is so low at this point.

Peter Khanahmadi:
What is an allowable false acceptance or false rejection rate:

JT (Joseph Taylor)
- we don't want to publish that
- but industry standards
	- < 2% false reject
	- < 1% false accept
	- id.me

A risk: the threat can shift.  See if fraudsters begin using VoIP now that we allow it.

Resolution (checking user's attributes against records):
- SSN
- Name
- DOB
- etc

resolution step (between doc-auth and phone check)

aamva:
hard fail on mismatch of first name, vs fuzzy match first name?

LN
- IV is NOT one of LN's best resolution products.
- It does like 10 checks, and all must pass.
- IDverse, etc are BETTER products from LN.

Per Courtney
- Get access to more attributes that the vendors are using.
- Being able to pull them out in an easy way.
- Also, using Python and outputting a google sheet.
- We don't have a DB to write it to.
- Pulling a week worth of data from the portal is really slow.
- Every new query becomes custom, even if it IS in redshift.
- It's just overly complex to get data.
- Decision memo in front of leadership team on moving to DataBricks as a data solution.