~/Developer/identity-devops-private/vars



The instances are failing fast enough that there's none running right now. Let's check if there's a cloud-init log specific to IDP instances in CloudWatch:

aws-vault exec sandbox-terraform -- aws logs describe-log-streams \
  --log-group-name "cameter_/var/log/syslog" \
  --order-by LastEventTime --descending --limit 10 \
  --query "logStreams\[*\].logStreamName" --output table

This will show us the log stream names — they usually include the instance ID, so we can find the IDP ones.
Then we can tail a specific stream:

aws-vault exec sandbox-terraform -- aws logs get-log-events \
  --log-group-name "cameter_/var/log/syslog" \
  --log-stream-name "<idp-stream-name>" \
  --limit 100 \
  --query "events\[*\].message" --output text


## Found it! The root cause is clear:

```
rake aborted!
attribute_encryption_key is required but is not present
```
## The IDP application is missing a required configuration value attribute_encryption_key in its application.yml. The create-sandbox script copied secrets from dev, but this key either:
1. Doesn't exist in the dev secrets
2. Wasn't copied properly
3. Needs to be generated specifically for your sandbox


## Check what's in your sandbox's application.yml:

```
aws-vault exec sandbox-terraform -- aws s3 cp \
  s3://login-gov.app-secrets.894947205914-us-west-2/cameter/idp/v1/application.yml - | grep -i attribute_encryption
```

## And compare to dev:

```
aws-vault exec sandbox-terraform -- aws s3 cp \
  s3://login-gov.app-secrets.894947205914-us-west-2/dev/idp/v1/application.yml - | grep -i attribute_encryption
```
## This will show if the key exists in dev and whether it was copied to your cameter environment.

_______________


That explains it. The solipet sandbox has static keys directly in the application.yml — not Secrets Manager references. This is the older approach.

Compare:

solipet: attribute_encryption_key: change-this-immediately-with-rake-secret-2086dfbd... (static value)
dev: attribute_encryption_key: \['secrets_manager', 'dev/idp/attribute_encryption_key'\] (Secrets Manager reference)
The solipet sandbox was likely created before the dev environment moved to Secrets Manager, so when its application.yml was copied from dev, the keys were still static values.

Now that dev uses Secrets Manager references, new sandboxes need to either:

Enable Secrets Manager (idp_secrets_manager_secrets_enabled = true) so the references work, or
Manually populate static keys in the application.yml
Your Options
Option A: Enable Secrets Manager (the "modern" approach)

Uncomment idp_secrets_manager_secrets_enabled = true
Re-run bin/create-sandbox -a -t -s
Commit/push to identity-devops-private
Option B: Use static keys (the "solipet" approach)

Simpler, no terraform changes needed
Just copy the static key format into your application.yml:
# Download your config
aws-vault exec sandbox-terraform -- aws s3 cp \
  s3://login-gov.app-secrets.894947205914-us-west-2/cameter/idp/v1/application.yml /tmp/cameter-app.yml

# Add the missing keys (you can copy solipet's or generate new ones)
# Then upload back
aws-vault exec sandbox-terraform -- aws s3 cp \
  /tmp/cameter-app.yml s3://login-gov.app-secrets.894947205914-us-west-2/cameter/idp/v1/application.yml

_________________________

identity-devops-private/vars/cameter.tfvars

uncomment:
idp_secrets_manager_secrets_enabled = true

Then run:
cd ~/Developer/identity-devops
bin/create-sandbox -a -t -s




Excellent! 🎉 The Secrets Manager fix worked. Your cameter sandbox IDP is now running.
Summary of what fixed it:
- Enabled idp_secrets_manager_secrets_enabled = true in tfvars
- Ran bin/create-sandbox -a -t -s which:
	- Task 1: Kept the \['secrets_manager', ...\] references in application.yml
	- Task 3: Created the KMS key and Secrets Manager secrets via Terraform
	- Task 4: Copied secret values from dev to cameter
Your sandbox should now be accessible:
- [https://idp.cameter.identitysandbox.gov](https://idp.cameter.identitysandbox.gov/)
- [https://dashboard.cameter.identitysandbox.gov](https://dashboard.cameter.identitysandbox.gov/)