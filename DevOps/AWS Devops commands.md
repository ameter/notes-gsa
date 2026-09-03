https://gitlab.login.gov/lg/identity-devops/-/wikis/Setting-Up-AWS-Vault

## Testing your configuration 

Try returning details about your own IAM role:
	aws-vault exec master -- aws sts get-caller-identity

This will list S3 buckets. You will have to replace the profile name with one you have. (Profiles available to you can be found with the aws-vault list command.)
	aws-vault exec sample-profile -- aws s3 ls
For example
	aws-vault exec sandbox-power -- aws s3 ls
	
## Rotating AWS keys

AWS recommends rotating Access/Secret Keys every 30 days, and not allowing keys to go longer than 90 days without rotation. To rotate your login-master AWS keys, simply run:

aws-vault rotate master


## Using aws-vault

The fastest way to run aws-vault commands is by using bin/awsv in the identity-devops repo.  To see the full list of commands/options available to you via flags:

bin/awsv -h

## Regular usage

If you want to specify an AWS account/profile when running a command, you can do it with aws-vault like this:


```
aws-vault exec <profile> -- <command>
```

The above executes any command — like aws, bundle exec, or tf-deploy — within the AWS vault context. A now-outdated alternative way to specify the profile is by prepending AWS_PROFILE=$SOMEPROFILE before the command.

Instead of exec, you can use login to open the AWS Console in your browser with a specific profile:


```
aws-vault login <profile>
```