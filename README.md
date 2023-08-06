Launch
Create connect file
Create a file in the root directory of this project.

Name this file "script-connect-aws.sh", it'll be ignored by git. (cf .gitignor)

#!/usr/bin/env bash
unset AWS_ACCESS_KEY_ID AWS_SECRET_ACCESS_KEY AWS_STS AWS_ACCESS_KEY_ID AWS_SECRET_ACCESS_KEY AWS_SECURITY_TOKEN AWS_SESSION_TOKEN

--This line contains multiple unset commands, each followed by the names of environment variables. The unset command is used to remove or unset the value of environment variables in the current shell session.
Unsetting these variables is useful when you want to clear any existing AWS credentials and tokens from the environment. This could be done, for example, when you want to ensure that your current shell session doesn't accidentally use any AWS credentials from a previous session or if you're switching between multiple AWS accounts or roles. After running this script, the mentioned environment variables will have their values removed from the current shell session, making them unavailable for any subsequent AWS-related operations until they are set again.


export USERNAME=***
export AWS_DEFAULT_REGION=eu-west-1
export AWS_ACCESS_KEY_ID=***
export AWS_SECRET_ACCESS_KEY=***
export ROLE_NAME=handson-serverless-role-name
export ACCOUNT_ARN=arn:aws:iam::***
export MFA_CODE=$1

--With these environment variables set and exported, any subsequent commands or scripts run in the same shell session will be able to access and use these values. Keep in mind that sensitive information like AWS credentials should be handled with care, and it's essential to maintain security best practices when working with AWS resources.

# Assume the IAM role with MFA and store the credentials in the aws_sts variable
aws_sts=$(aws sts assume-role --role-arn $account_arn:role/$role_name --serial-number $account_arn:mfa/$username --query 'Credentials.[AccessKeyId,SecretAccessKey,SessionToken]' --output text)

# Extract the individual components of the credentials from the aws_sts variable
IFS=$'\t' read -r -a aws_sts_array <<< "$aws_sts"

# Export the credentials as environment variables
export AWS_ACCESS_KEY_ID="${aws_sts_array[0]}"
export AWS_SECRET_ACCESS_KEY="${aws_sts_array[1]}"
export AWS_SESSION_TOKEN="${aws_sts_array[2]}"

--We use the read command with the -a option to read the values from the aws_sts variable and store them in an array aws_sts_array. The values are separated by tabs (specified by IFS=$'\t').
--The array elements aws_sts_array[0], aws_sts_array[1], and aws_sts_array[2] correspond to the Access Key ID, Secret Access Key, and Session Token, respectively.
--We use the export command to set these values as environment variables so that they can be used in subsequent AWS CLI commands or other AWS-related operations.


You have to change:

export USERNAME with your AWS login
export AWS_ACCESS_KEY_ID with your AWS access key
export AWS_SECRET_ACCESS_KEY with your AWS secret access key
export ACCOUNT_ARN=arn:aws:iam::*** : replace *** by your AWS account id
You can connect to AWS console with your login and assume-role

Get your credential
source ./script-connect-aws.sh LASTMFACODE
Create infrastructure
The first time you use it localy, please follow this step:
python ci-cd/init.py
Launch script
python ci-cd/start.py
Delete infrastructure
python ci-cd/stop.py
