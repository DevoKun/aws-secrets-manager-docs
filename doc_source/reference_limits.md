# Limits of AWS Secrets Manager<a name="reference_limits"></a>

This section specifies limits that affect AWS Secrets Manager\.

## Limits on Names<a name="reference_limits_names"></a>

The following are restrictions on names that you create in AWS Secrets Manager \(including names of secrets\):
+ They must be composed of Unicode characters\.
+ They must not exceed 256 characters in length\.

## Maximum and Minimum Values<a name="reference_limits_max-min"></a>

The following are the default maximums for entities in AWS Secrets Manager:


****  

|  |  | 
| --- |--- |
| Max number of secrets in an AWS account | 40,000 | 
| Max number of versions in a secret | \~100  | 
| Max number of labels you can attach across all versions of a secret | 20 | 
| Max number of versions a label can be attached to at the same time | 1 | 
| Maximum length of a secret | 4,096 characters | 
| Maximum length of a resource\-based policy \- JSON text | 4,096 characters | 

## Maximum Rate Limits<a name="reference_limits_rates"></a>

The following are the rate limits for AWS Secrets Manager:


****  

|  |  | 
| --- |--- |
| Request type | Number of operations permitted per second | 
| Overall API limit for the account | 1,000 | 
|  DescribeSecret GetSecretValue  | 700 | 
|  PutResourcePolicy GetResourcePolicy DeleteResourcePolicy CreateSecret UpdateSecret UpdateSecretVersionStage PutSecretValue DeleteSecret RestoreSecret RotateSecret CancelRotateSecret AssociateSecretLabels DisassociateSecretLabels GetRandomPassword  | 40 | 
|  TagResource UntagResource  | 20 | 
|  ListSecrets ListSecretVersionIds  | 5 | 
