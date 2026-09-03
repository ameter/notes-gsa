~/Developer/identity-devops % aws-vault exec sandbox-terraform -- aws autoscaling describe-auto-scaling-groups \
  --query "AutoScalingGroups\[?contains(AutoScalingGroupName, 'cameter')\].AutoScalingGroupName" \
  --output table

---------------------------
|DescribeAutoScalingGroups|
+-------------------------+
|  cameter-idp            |
|  cameter-migration      |
|  cameter-outboundproxy  |
|  cameter-pivcac         |
|  cameter-portal         |
|  cameter-worker         |
+-------------------------+


## recycle sandbox idp

aws-vault exec sandbox-terraform -- aws autoscaling start-instance-refresh \
  --auto-scaling-group-name cameter-idp \
  --preferences '{"MinHealthyPercentage": 0}'


instance refresh started. This will replace the IDP instance with a fresh one that loads the new service_providers.yml.

Wait about 5-8 minutes, then

## check the status:

aws-vault exec sandbox-terraform -- aws autoscaling describe-instance-refreshes \
  --auto-scaling-group-name cameter-idp \
  --query "InstanceRefreshes\[0\].\[Status,PercentageComplete\]" \
  --output text