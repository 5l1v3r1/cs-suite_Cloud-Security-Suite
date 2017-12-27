# Prowler: AWS CIS Benchmark Tool

## Table of Contents  
- [Description](#description)
- [Features](#features)  
- [Requirements](#requirements)  
- [Usage](#usage)
- [Fix](#fix)
- [Screenshots](#screenshots)
- [Troubleshooting](#troubleshooting)
- [Extras](#extras)

## Description

Tool based on AWS-CLI commands for AWS account security assessment and hardening, following guidelines of the [CIS Amazon Web Services Foundations Benchmark 1.1 ](https://benchmarks.cisecurity.org/tools2/amazon/CIS_Amazon_Web_Services_Foundations_Benchmark_v1.1.0.pdf)

## Features

It covers hardening and security best practices for all AWS regions related to:

- Identity and Access Management (24 checks)
- Logging (8 checks)
- Monitoring (15 checks)
- Networking (5 checks)
- Extra checks (5 checks) *see Extras section

For a comprehesive list and resolution look at the guide on the link above.

With Prowler you can:
- get a colourish or monochrome report
- a CSV format report for diff
- run specific checks without having to run the entire report
- check multiple AWS accounts in parallel

## Requirements
This script has been written in bash using AWS-CLI and it works in Linux and OSX.

- Make sure your AWS-CLI is installed on your workstation, with Python pip already installed:
```
pip install awscli
```
Or install it using "brew", "apt", "yum" or manually from https://aws.amazon.com/cli/

- Previous steps, from your workstation:
```
git clone https://github.com/Alfresco/prowler
cd prowler
```

- Make sure you have properly configured your AWS-CLI with a valid Access Key and Region:
```
aws configure
```

- Make sure your Secret and Access Keys are associated to a user with proper permissions to do all checks. To make sure add SecurityAuditor default policy to your user. Policy ARN is

```
arn:aws:iam::aws:policy/SecurityAudit
```
> In some cases you may need more list or get permissions in some services, look at the Troubleshooting section for a more comprehensive policy if you find issues with the default SecurityAudit policy.

## Usage

1 - Run the prowler.sh command without options (it will use your environment variable credentials if exist or default in ~/.aws/credentials file and run checks over all regions when needed, default region is us-east-1):

```
./prowler
```

2 - For custom AWS-CLI profile and region, use the following: (it will use your custom profile and run checks over all regions when needed):

```
./prowler -p custom-profile -r us-east-1
```

3 - For a single check use option -c:

```
./prowler -c check310
```
or for custom profile and region
```
./prowler -p custom-profile -r us-east-1 -c check11
```
or for a group of checks use group name:
```
./prowler -c check3
```

Valid check numbers are based on the AWS CIS Benchmark guide, so 1.1 is check11 and 3.10 is check310

4 - If you want to save your report for later analysis:
```
./prowler > prowler-report.txt
```
or if you want a colored HTML report do:
```
pip install ansi2html
./prowler | ansi2html -la > report.html
```
or if you want a pipe-delimited report file, do:
```
./prowler -M csv > output.psv
```

5 - To perform an assessment based on CIS Profile Definitions you can use level1 or level2 with `-c` flag, more information about this [here, page 8](https://d0.awsstatic.com/whitepapers/compliance/AWS_CIS_Foundations_Benchmark.pdf):
```
./prowler -c level1
```

6 - If you want to run Prowler to check multiple AWS accounts in parallel (runs up to 4 simultaneously `-P 4`):

```
grep -E '^\[([0-9A-Aa-z_-]+)\]'  ~/.aws/credentials | tr -d '][' | shuf |  \
xargs -n 1 -L 1 -I @ -r -P 4 ./prowler -p @ -M csv  2> /dev/null  >> all-accounts.csv
```

7 - For help use:

```
./prowler -h

USAGE:
      prowler -p <profile> -r <region> [ -h ]
  Options:
      -p <profile>        specify your AWS profile to use (i.e.: default)
      -r <region>         specify an AWS region to direct API requests to (i.e.: us-east-1)
      -c <checknum>       specify a check number or group from the AWS CIS benchmark (i.e.: check11 for check 1.1, check3 for entire section 3 or level1 for CIS Level 1 Profile Definitions)
      -f <filterregion>   specify an AWS region to run checks against (i.e.: us-west-1)
      -m <maxitems>       specify the maximum number of items to return for long-running requests (default: 100)
      -M <mode>           output mode: text (defalut), mono, csv (separator is ","; data is on stdout; progress on stderr)
      -k                  keep the credential report
      -n                  show check numbers to sort easier (i.e.: 1.01 instead of 1.1)
      -h                  this help

```
## Fix:
 Check your report and fix the issues following all specific guidelines per check in https://benchmarks.cisecurity.org/tools2/amazon/CIS_Amazon_Web_Services_Foundations_Benchmark_v1.1.0.pdf

## Screenshots

- Sample screenshot of report first lines:
 <img width="1125" alt="screenshot 2016-09-13 16 05 42" src="https://cloud.githubusercontent.com/assets/3985464/18489640/50fe6824-79cc-11e6-8a9c-e788b88a8a6b.png">

- Sample screnshot of single check for check 3.3:
<img width="1006" alt="screenshot 2016-09-14 13 20 46" src="https://cloud.githubusercontent.com/assets/3985464/18522590/a04ca9a6-7a7e-11e6-8730-b545c9204990.png">

- Sample of a full report:

```
$ ./prowler
                          _
  _ __  _ __ _____      _| | ___ _ __
 | '_ \| '__/ _ \ \ /\ / / |/ _ \ '__|
 | |_) | | | (_) \ V  V /| |  __/ |
 | .__/|_|  \___/ \_/\_/ |_|\___|_|
 |_| CIS based AWS Account Hardening Tool


Date: Wed Sep 14 13:30:13 EDT 2016

This report is being generated using credentials below:

AWS-CLI Profile: [default] AWS Region: [us-east-1]

--------------------------------------------------------------------------------------
|                                  GetCallerIdentity                                 |
+--------------+-------------------------------------------+-------------------------+
|    Account   |                    Arn                    |         UserId          |
+--------------+-------------------------------------------+-------------------------+
|  XXXXXXXXXXXX|  arn:aws:iam::XXXXXXXXXXXX:user/toni      |  XXXXXXXXXXXXXXXXXXXXX  |
+--------------+-------------------------------------------+-------------------------+

Colors Code for results:  INFORMATIVE, OK (RECOMMENDED VALUE),  CRITICAL (FIX REQUIRED)


Generating AWS IAM Credential Report....COMPLETE


 1 Identity and Access Management *********************************

 1.1 Avoid the use of the root account (Scored). Last time root account was used
     (password last used, access_key_1_last_used, access_key_2_last_used):
      2016-08-11T20:59:27+00:00, N/A, N/A

 1.2 Ensure multi-factor authentication (MFA) is enabled for all IAM users that have a console password (Scored)
     List of users with Password enabled but MFA disabled:
      toni

 1.3 Ensure credentials unused for 90 days or greater are disabled (Scored)
     User list:
      toni

 1.4 Ensure access keys are rotated every 90 days or less (Scored)
     Users with access key 1 older than 90 days:
      <root_account>
     Users with access key 2 older than 90 days:

 1.5 Ensure IAM password policy requires at least one uppercase letter (Scored)
      FALSE

 1.6 Ensure IAM password policy require at least one lowercase letter (Scored)
      FALSE

 1.7 Ensure IAM password policy require at least one symbol (Scored)
      FALSE

 1.8 Ensure IAM password policy require at least one number (Scored)
      FALSE

 1.9 Ensure IAM password policy requires minimum length of 14 or greater (Scored)
      FALSE

 1.10 Ensure IAM password policy prevents password reuse (Scored)
      FALSE

 1.11 Ensure IAM password policy expires passwords within 90 days or less (Scored)
      FALSE

 1.12 Ensure no root account access key exists (Scored)
      Found access key 1
      OK  No access key 2 found

 1.13 Ensure hardware MFA is enabled for the root account (Scored)
      OK

 1.14 Ensure security questions are registered in the AWS account (Not Scored)
      No command available for check 1.14
      Login to the AWS Console as root, click on the Account
      Name -> My Account -> Configure Security Challenge Questions

 1.15 Ensure IAM policies are attached only to groups or roles (Scored)
      Users with policy attached to them instead to groups: (it may take few seconds...)
      toni


 2 Logging ********************************************************

 2.1 Ensure CloudTrail is enabled in all regions (Scored)
      FALSE

 2.2 Ensure CloudTrail log file validation is enabled (Scored)
      FALSE

 2.3 Ensure the S3 bucket CloudTrail logs to is not publicly accessible (Scored)
      WARNING! CloudTrail bucket doesn't exist!

 2.4 Ensure CloudTrail trails are integrated with CloudWatch Logs (Scored)
      WARNING! No CloudTrail trails found!

 2.5 Ensure AWS Config is enabled in all regions (Scored)
      WARNING! Region ap-south-1 has AWS Config disabled or not configured
      WARNING! Region eu-west-1 has AWS Config disabled or not configured
      WARNING! Region ap-southeast-1 has AWS Config disabled or not configured
      WARNING! Region ap-southeast-2 has AWS Config disabled or not configured
      WARNING! Region eu-central-1 has AWS Config disabled or not configured
      WARNING! Region ap-northeast-2 has AWS Config disabled or not configured
      WARNING! Region ap-northeast-1 has AWS Config disabled or not configured
      WARNING! Region us-east-1 has AWS Config disabled or not configured
      WARNING! Region sa-east-1 has AWS Config disabled or not configured
      WARNING! Region us-west-1 has AWS Config disabled or not configured
      WARNING! Region us-west-2 has AWS Config disabled or not configured

 2.6 Ensure S3 bucket access logging is enabled on the CloudTrail S3 bucket (Scored)
      WARNING! CloudTrail bucket doesn't exist!

 2.7 Ensure CloudTrail logs are encrypted at rest using KMS CMKs (Scored)
      WARNING! CloudTrail bucket doesn't exist!

 2.8 Ensure rotation for customer created CMKs is enabled (Scored)
      Region ap-south-1 doesn't have encryption keys
      Region eu-west-1 doesn't have encryption keys
      Region ap-southeast-1 doesn't have encryption keys
      Region ap-southeast-2 doesn't have encryption keys
      Region eu-central-1 doesn't have encryption keys
      Region ap-northeast-2 doesn't have encryption keys
      Region ap-northeast-1 doesn't have encryption keys
      WARNING! Key a0e988df-bc84-423f-996c-XXXX in Region us-east-1 is not set to rotate!
      Region sa-east-1 doesn't have encryption keys
      Region us-west-1 doesn't have encryption keys
      Region us-west-2 doesn't have encryption keys


 3 Monitoring *****************************************************

 3.1 Ensure a log metric filter and alarm exist for unauthorized API calls (Scored)
      WARNING! No CloudWatch group found, no metric filters or alarms associated

 3.2 Ensure a log metric filter and alarm exist for Management Console sign-in without MFA (Scored)
      WARNING! No CloudWatch group found, no metric filters or alarms associated

 3.3 Ensure a log metric filter and alarm exist for usage of root account (Scored)
      WARNING! No CloudWatch group found, no metric filters or alarms associated

 3.4 Ensure a log metric filter and alarm exist for IAM policy changes (Scored)
      WARNING! No CloudWatch group found, no metric filters or alarms associated

 3.5 Ensure a log metric filter and alarm exist for CloudTrail configuration changes (Scored)
      WARNING! No CloudWatch group found, no metric filters or alarms associated

 3.6 Ensure a log metric filter and alarm exist for AWS Management Console authentication failures (Scored)
      WARNING! No CloudWatch group found, no metric filters or alarms associated

 3.7 Ensure a log metric filter and alarm exist for disabling or scheduled deletion of customer created CMKs (Scored)
      WARNING! No CloudWatch group found, no metric filters or alarms associated

 3.8 Ensure a log metric filter and alarm exist for S3 bucket policy changes (Scored)
      WARNING! No CloudWatch group found, no metric filters or alarms associated

 3.9 Ensure a log metric filter and alarm exist for AWS Config configuration changes (Scored)
      WARNING! No CloudWatch group found, no metric filters or alarms associated

 3.10 Ensure a log metric filter and alarm exist for security group changes (Scored)
      WARNING! No CloudWatch group found, no metric filters or alarms associated

 3.11 Ensure a log metric filter and alarm exist for changes to Network Access Control Lists (NACL) (Scored)
      WARNING! No CloudWatch group found, no metric filters or alarms associated

 3.12 Ensure a log metric filter and alarm exist for changes to network gateways (Scored)
      WARNING! No CloudWatch group found, no metric filters or alarms associated

 3.13 Ensure a log metric filter and alarm exist for route table changes (Scored)
      WARNING! No CloudWatch group found, no metric filters or alarms associated

 3.14 Ensure a log metric filter and alarm exist for VPC changes (Scored)
      WARNING! No CloudWatch group found, no metric filters or alarms associated

 3.15 Ensure security contact information is registered (Scored)
      No command available for check 3.15
      Login to the AWS Console, click on My Account
      Go to Alternate Contacts -> make sure Security section is filled

 3.16 Ensure appropriate subscribers to each SNS topic (Not Scored)
      Region ap-south-1 doesn't have topics
      Region eu-west-1 doesn't have topics
      Region ap-southeast-1 doesn't have topics
      Region ap-southeast-2 doesn't have topics
      Region eu-central-1 doesn't have topics
      Region ap-northeast-2 doesn't have topics
      Region ap-northeast-1 doesn't have topics
      Region us-east-1 doesn't have topics
      Region sa-east-1 doesn't have topics
      Region us-west-1 doesn't have topics
      Region us-west-2 doesn't have topics


 4 Networking **************************************************

 4.1 Ensure no security groups allow ingress from 0.0.0.0/0 to port 22 (Scored)
      OK, No Security Groups found in ap-south-1 with port 22 TCP open to 0.0.0.0/0
      OK, No Security Groups found in eu-west-1 with port 22 TCP open to 0.0.0.0/0
      OK, No Security Groups found in ap-southeast-1 with port 22 TCP open to 0.0.0.0/0
      OK, No Security Groups found in ap-southeast-2 with port 22 TCP open to 0.0.0.0/0
      OK, No Security Groups found in eu-central-1 with port 22 TCP open to 0.0.0.0/0
      OK, No Security Groups found in ap-northeast-2 with port 22 TCP open to 0.0.0.0/0
      OK, No Security Groups found in ap-northeast-1 with port 22 TCP open to 0.0.0.0/0
      OK, No Security Groups found in us-east-1 with port 22 TCP open to 0.0.0.0/0
      OK, No Security Groups found in sa-east-1 with port 22 TCP open to 0.0.0.0/0
      OK, No Security Groups found in us-west-1 with port 22 TCP open to 0.0.0.0/0
      OK, No Security Groups found in us-west-2 with port 22 TCP open to 0.0.0.0/0

 4.2 Ensure no security groups allow ingress from 0.0.0.0/0 to port 3389 (Scored)
      OK, No Security Groups found in ap-south-1 with port 3389 TCP open to 0.0.0.0/0
      OK, No Security Groups found in eu-west-1 with port 3389 TCP open to 0.0.0.0/0
      OK, No Security Groups found in ap-southeast-1 with port 3389 TCP open to 0.0.0.0/0
      OK, No Security Groups found in ap-southeast-2 with port 3389 TCP open to 0.0.0.0/0
      OK, No Security Groups found in eu-central-1 with port 3389 TCP open to 0.0.0.0/0
      OK, No Security Groups found in ap-northeast-2 with port 3389 TCP open to 0.0.0.0/0
      OK, No Security Groups found in ap-northeast-1 with port 3389 TCP open to 0.0.0.0/0
      OK, No Security Groups found in us-east-1 with port 3389 TCP open to 0.0.0.0/0
      OK, No Security Groups found in sa-east-1 with port 3389 TCP open to 0.0.0.0/0
      OK, No Security Groups found in us-west-1 with port 3389 TCP open to 0.0.0.0/0
      OK, No Security Groups found in us-west-2 with port 3389 TCP open to 0.0.0.0/0

 4.3 Ensure VPC Flow Logging is Enabled in all Applicable Regions (Scored)
      WARNING! no VPCFlowLog has been found in Region ap-south-1
      WARNING! no VPCFlowLog has been found in Region eu-west-1
      WARNING! no VPCFlowLog has been found in Region ap-southeast-1
      WARNING! no VPCFlowLog has been found in Region ap-southeast-2
      WARNING! no VPCFlowLog has been found in Region eu-central-1
      WARNING! no VPCFlowLog has been found in Region ap-northeast-2
      WARNING! no VPCFlowLog has been found in Region ap-northeast-1
      WARNING! no VPCFlowLog has been found in Region us-east-1
      WARNING! no VPCFlowLog has been found in Region sa-east-1
      WARNING! no VPCFlowLog has been found in Region us-west-1
      WARNING! no VPCFlowLog has been found in Region us-west-2

 4.4 Ensure the default security group restricts all traffic (Scored)
      WARNING! Default Security Groups found that allow 0.0.0.0 IN or OUT traffic in Region ap-south-1
      WARNING! Default Security Groups found that allow 0.0.0.0 IN or OUT traffic in Region eu-west-1
      WARNING! Default Security Groups found that allow 0.0.0.0 IN or OUT traffic in Region ap-southeast-1
      WARNING! Default Security Groups found that allow 0.0.0.0 IN or OUT traffic in Region ap-southeast-2
      WARNING! Default Security Groups found that allow 0.0.0.0 IN or OUT traffic in Region eu-central-1
      WARNING! Default Security Groups found that allow 0.0.0.0 IN or OUT traffic in Region ap-northeast-2
      WARNING! Default Security Groups found that allow 0.0.0.0 IN or OUT traffic in Region ap-northeast-1
      OK, no Default Security Groups open to 0.0.0.0 found in Region us-east-1
      WARNING! Default Security Groups found that allow 0.0.0.0 IN or OUT traffic in Region sa-east-1
      WARNING! Default Security Groups found that allow 0.0.0.0 IN or OUT traffic in Region us-west-1
      OK, no Default Security Groups open to 0.0.0.0 found in Region us-west-2

 - For more information and reference:
   https://d0.awsstatic.com/whitepapers/compliance/AWS_CIS_Foundations_Benchmark.pdf
```

## Troubleshooting

### STS expired token
If you are using an STS token for AWS-CLI and your session is expired you probably get this error:

```
A client error (ExpiredToken) occurred when calling the GenerateCredentialReport operation: The security token included in the request is expired
```
To fix it, please renew your token by authenticating again to the AWS API.

### Custom IAM Policy
Instead of using default policy SecurityAudit for the account you use for checks you may need to create a custom policy with a few more permissions (get and list, not change!) here you go a good example for a "ProwlerPolicyReadOnly":

```
{
    "Version": "2012-10-17",
    "Statement": [{
        "Action": [
            "acm:describecertificate",
            "acm:listcertificates",
            "autoscaling:describe*",
            "cloudformation:describestack*",
            "cloudformation:getstackpolicy",
            "cloudformation:gettemplate",
            "cloudformation:liststack*",
            "cloudfront:get*",
            "cloudfront:list*",
            "cloudtrail:describetrails",
            "cloudtrail:gettrailstatus",
            "cloudtrail:listtags",
            "cloudwatch:describe*",
            "cloudwatchlogs:describeloggroups",
            "cloudwatchlogs:describemetricfilters",
            "codecommit:batchgetrepositories",
            "codecommit:getbranch",
            "codecommit:getobjectidentifier",
            "codecommit:getrepository",
            "codecommit:list*",
            "codedeploy:batch*",
            "codedeploy:get*",
            "codedeploy:list*",
            "config:deliver*",
            "config:describe*",
            "config:get*",
            "datapipeline:describeobjects",
            "datapipeline:describepipelines",
            "datapipeline:evaluateexpression",
            "datapipeline:getpipelinedefinition",
            "datapipeline:listpipelines",
            "datapipeline:queryobjects",
            "datapipeline:validatepipelinedefinition",
            "directconnect:describe*",
            "dynamodb:listtables",
            "ec2:describe*",
            "ecs:describe*",
            "ecs:list*",
            "elasticache:describe*",
            "elasticbeanstalk:describe*",
            "elasticloadbalancing:describe*",
            "elasticmapreduce:describejobflows",
            "elasticmapreduce:listclusters",
            "es:describeelasticsearchdomainconfig",
            "es:listdomainnames",
            "firehose:describe*",
            "firehose:list*",
            "glacier:listvaults",
            "iam:generatecredentialreport",
            "iam:get*",
            "iam:list*",
            "kms:describe*",
            "kms:get*",
            "kms:list*",
            "lambda:getpolicy",
            "lambda:listfunctions",
            "logs:DescribeMetricFilters",
            "rds:describe*",
            "rds:downloaddblogfileportion",
            "rds:listtagsforresource",
            "redshift:describe*",
            "route53:getchange",
            "route53:getcheckeripranges",
            "route53:getgeolocation",
            "route53:gethealthcheck",
            "route53:gethealthcheckcount",
            "route53:gethealthchecklastfailurereason",
            "route53:gethostedzone",
            "route53:gethostedzonecount",
            "route53:getreusabledelegationset",
            "route53:listgeolocations",
            "route53:listhealthchecks",
            "route53:listhostedzones",
            "route53:listhostedzonesbyname",
            "route53:listresourcerecordsets",
            "route53:listreusabledelegationsets",
            "route53:listtagsforresource",
            "route53:listtagsforresources",
            "route53domains:getdomaindetail",
            "route53domains:getoperationdetail",
            "route53domains:listdomains",
            "route53domains:listoperations",
            "route53domains:listtagsfordomain",
            "s3:getbucket*",
            "s3:getlifecycleconfiguration",
            "s3:getobjectacl",
            "s3:getobjectversionacl",
            "s3:listallmybuckets",
            "sdb:domainmetadata",
            "sdb:listdomains",
            "ses:getidentitydkimattributes",
            "ses:getidentityverificationattributes",
            "ses:listidentities",
            "ses:listverifiedemailaddresses",
            "ses:sendemail",
            "sns:gettopicattributes",
            "sns:listsubscriptionsbytopic",
            "sns:listtopics",
            "sqs:getqueueattributes",
            "sqs:listqueues",
            "tag:getresources",
            "tag:gettagkeys"
        ],
        "Effect": "Allow",
        "Resource": "*"
    }]
}
```

### Incremental IAM Policy

Alternatively, here is a policy which defines the permissions which are NOT present in the AWS Managed SecurityAudit policy. Attach both this policy and the AWS Managed SecurityAudit policy to the group and you're good to go.  

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Action": [
        "acm:DescribeCertificate",
        "acm:ListCertificates",
        "cloudwatchlogs:describeLogGroups",
        "cloudwatchlogs:DescribeMetricFilters",
        "es:DescribeElasticsearchDomainConfig",
        "ses:GetIdentityVerificationAttributes",
        "sns:ListSubscriptionsByTopic"
      ],
      "Effect": "Allow",
      "Resource": "*"
    }
  ]
}
```

### Bootstrap Script

Quick bash script to set up a "prowler" IAM user and "SecurityAudit" group with the required permissions. To run the script below, you need user with administrative permissions; set the AWS_DEFAULT_PROFILE to use that account.

```
export AWS_DEFAULT_PROFILE=default
export ACCOUNT_ID=$(aws sts get-caller-identity --query 'Account' | tr -d '"')
aws iam create-group --group-name SecurityAudit
aws iam create-policy --policy-name ProwlerAuditAdditions --policy-document file://$(pwd)/prowler-policy-additions.json
aws iam attach-group-policy --group-name SecurityAudit --policy-arn arn:aws:iam::aws:policy/SecurityAudit
aws iam attach-group-policy --group-name SecurityAudit --policy-arn arn:aws:iam::${ACCOUNT_ID}:policy/ProwlerAuditAdditions
aws iam create-user --user-name prowler
aws iam add-user-to-group --user-name prowler --group-name SecurityAudit
aws iam create-access-key --user-name prowler
unset ACCOUNT_ID AWS_DEFAULT_PROFILE
```

The `aws iam create-access-key` command will output the secret access key and the key id; keep these somewhere safe, and add them to ~/.aws/credentials with an appropriate profile name to use them with prowler. This is the only time they secret key will be shown.  If you loose it, you will need to generate a replacement.

## Extras
We are adding additional checks to improve the information gather from each account, these checks are out of the scope of the CIS benchmark for AWS but we consider them very helpful to get to know each AWS account set up and find issues on it.
At this momment we have 5 extra checks:

- 7.1 (`extra71`) Ensure users with AdministratorAccess policy have MFA tokens enabled (Not Scored) (Not part of CIS benchmark)
- 7.2 (`extra72`) Ensure there are no EBS Snapshots set as Public (Not Scored) (Not part of CIS benchmark)
- 7.3 (`extra73`) Ensure there are no S3 buckets open to the Everyone or Any AWS user (Not Scored) (Not part of CIS benchmark)
- 7.4 (`extra74`) Ensure there are no Security Groups without ingress filtering being used (Not Scored) (Not part of CIS benchmark)
- 7.5 (`extra75`) Ensure there are no Security Groups not being used (Not Scored) (Not part of CIS benchmark)

```
./prowler -c extras
```
or to run just one of the checks, to see if you have S3 buckets open:
```
./prowler -c extraNUMBER
```
