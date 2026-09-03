Your sandbox uses telephony_adapter: pinpoint (the real SMS service), not test. So OTP codes won't be prefilled either.

To make your sandbox easier to test, you need to change two settings:
enable_load_testing_mode: "true" - for email confirmation bypass
telephony_adapter: test - for OTP code prefilling

Let's download, modify, and re-upload your config:

## Get Current Config


```
aws-vault exec sandbox-terraform -- aws s3 cp s3://login-gov.app-secrets.894947205914-us-west-2/cameter/idp/v1/application.yml ~/temp/cameter-app.yml
```

## For testing

## Bypass Phone OTPs

telephony_adapter: pinpoint	# uses real phone OTPs
telephony_adapter: test		

What it does: Uses a fake SMS/voice adapter instead of real AWS Pinpoint. On *.identitysandbox.gov domains with this setting, OTP codes will be pre-filled in the form automatically - you just click submit instead of waiting for a real SMS.


## Bypass e-mail confirmation

Add this line (anywhere in the production: block, e.g., near other enable_* settings):

enable_load_testing_mode: "true"

What it does: Shows a "CONFIRM NOW" link on the email verification page so you can confirm your email without actually receiving the email.


## Apply changes

## After editing, upload it back:

```
aws-vault exec sandbox-terraform -- aws s3 cp /tmp/cameter-app.yml s3://login-gov.app-secrets.894947205914-us-west-2/cameter/idp/v1/application.yml
```

## Then trigger an instance refresh to pick up the changes:

```
aws-vault exec sandbox-terraform -- aws autoscaling start-instance-refresh \
  --auto-scaling-group-name cameter-idp \
  --preferences '{"MinHealthyPercentage": 0}'
```