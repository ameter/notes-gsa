I want to run from local development though on some new code... Like wanting to see if the file for a report can be written/read to S3

\[7:08 AM\]
But the No route to host makes me think our GFEs are blocked from accessing AWS unless we use aws-vault exec.

\[7:08 AM\]
I think the alternative would be to test on a dev sandbox

davida marion (login.gov - brooklyn - she/they)  \[7:10 AM\]
okay yeah i thiiiink aws-vault exec prod-power -- bundle exec rails runner rails c should get you into a rails console with the privs that you need

Ambuj Neupane (Login/DC)  \[7:36 AM\]
we've had this problem forever and ever with reporting. It depends on what you want to test:
- if you want to validate code, you can run tests in sandbox envs with rails c
- if you want to validate data, you can connect to the warehouse locally and write the equivalent SQL code to cloudwatch
- if you want to test cloudwatch data output with prod data specifically (since we don't have good representative synthetic data in lower environments), then yeah you would need to run Davida's command and insert in your variables for a given report


[We wrote a short guide a while back](https://docs.google.com/document/d/1ZHoXDatZ3-CDURIXzhv2bfBOdUG5aENn0euE9owqhgo/edit?tab=t.0), but it needs updating. [@davida marion (login.gov - brooklyn - she/they)](https://gsa.enterprise.slack.com/team/U01J862HCLA) also was the source of knowledge there so do what they say 

Doug Price (login.gov, bwi, he)  \[2:17 PM\]
you *can* also run aws-vault exec <role> — /bin/zsh, then your shell is within the vault. Run rails in there? Maybe?