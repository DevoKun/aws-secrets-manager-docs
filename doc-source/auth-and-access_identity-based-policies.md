# Using Identity\-based Policies \(IAM Policies\) for Secrets Manager<a name="auth-and-access_identity-based-policies"></a>

As an administrator of an account, you can control access to the AWS resources in the account by attaching permissions policies to IAM identities \(users, groups, and roles\)\. When granting permissions, you decide who is getting the permissions, the resources they get permissions to, and the specific actions that you want to allow on those resources\. If you grant permissions to a role, users in other accounts that you specify can assume that role\.

By default, a user \(or role\) has no permissions of any kind\. You must explicitly grant any permissions by using a policy\. If a permission isn't explicitly granted, then it's implicitly denied\. If a permission is explicitly denied, then that overrules any other policy that might have allowed it\. In other words, a user has only those permissions that are explicitly granted and that are not explicitly denied\.

For an overview of the basic elements for policies, see [Managing Access to Resources with Policies](auth-and-access_overview.md#auth-and-access_resource-access)\.

**Topics**
+ [AWS Managed Policy for Secrets Manager](#available-managed-policies)
+ [Granting Full Secrets Manager Administrator Permissions to a User](#permissions_grant-admin-actions)
+ [For the Consuming Application: Granting Read Access to One Secret](#permissions_grant-get-secret-value-to-one-secret)
+ [Limiting Access to Specific Actions](#permissions_grant-limited-actions)
+ [Limiting Access to Specific Secrets](#permissions_grant-limited-resources)
+ [Limiting Access to Secrets That Have Specific Staging Labels or Tags](#permissions_grant-limited-condition)
+ [Granting a Rotation Function Permission to Access a Separate Master Secret](#permissions-grant-rotation-role-access-to-master-secret)

## AWS Managed Policy for Secrets Manager<a name="available-managed-policies"></a>

AWS Secrets Manager provides the following AWS managed policy to make granting permissions easier\. Choose the link to view it in the IAM console\.
+ [SecretsManagerReadWrite](https://console.aws.amazon.com/iam/home?#/policies/arn:aws:iam::aws:policy/SecretsManagerReadWrite) – This policy grants the access that's required to administer Secrets Manager, except that it doesn't include the IAM permissions required to create roles and attach policies to those roles\. For those permissions, attach the [IAMFullAccess](https://console.aws.amazon.com/iam/home?#/policies/arn:aws:iam::aws:policy/IAMFullAccess) managed policy\. For instructions, see the following section [Granting Full Secrets Manager Administrator Permissions to a User](#permissions_grant-admin-actions)\.

## Granting Full Secrets Manager Administrator Permissions to a User<a name="permissions_grant-admin-actions"></a>

To be a Secrets Manager administrator, you must have permissions in several services\. We recommended that you don't enable Secrets Manager as an end\-user service that enables your users to create and manage their own secrets\. The permissions that are required to enable rotation grant significant permissions that standard users shouldn't have\. Instead, Secrets Manager is intended to be a service that's managed by trusted administrators\. The intended benefit is that the end user no longer needs to handle the credentials directly or embed them in code\.

**Warning**  
When you enable rotation, Secrets Manager creates a Lambda function and an IAM role that it attaches to the function\. This requires several IAM permissions that should be granted only to trusted individuals\. Therefore, the managed policy for Secrets Manager purposefully *does not* include these IAM permissions\. Instead, you must explicitly choose to assign the `IAMFullAccess` managed policy, in addition to the `SecretsManagerReadWrite` managed policy to create a full Secrets Manager administrator\.  
Granting access with only the `SecretsManagerReadWrite` policy enables an IAM user to create and manage secrets, but that user can't create and configure the Lambda rotation functions that are required to enable rotation\.

Complete the following steps to grant full Secrets Manager administrator permissions to an IAM user, group, or role in your account\. In this example, you don't create a new policy\. Instead, you attach an AWS managed policy that's preconfigured with the permissions\.

**To grant full administrator permissions to an IAM user, group, or role**

1. Sign in to the Identity and Access Management \(IAM\) console at [https://console\.aws\.amazon\.com/iam/](https://console.aws.amazon.com/iam/) as a user who has permissions to attach IAM policies to other IAM users\.

   In the IAM console, navigate to **Policies**\.

1. For **Filter: Policy type**, choose **AWS managed**, and then in the **Search** box, start typing **SecretsManagerReadWrite** until you can see the policy in the list\.

1. Choose the **SecretsManagerReadWrite** policy name\.

1. Choose the **Attached entities** tab, and then choose **Attach**\.

1. Check the box next to the users, groups, or roles that you want to make a Secrets Manager administrator\.

1. Choose **Attach policy**\.

1. Repeat steps 1 through 6 to also attach the **IAMFullAccess** policy\.

The selected users, groups, and roles can immediately begin performing tasks in Secrets Manager\.

## For the Consuming Application: Granting Read Access to One Secret<a name="permissions_grant-get-secret-value-to-one-secret"></a>

When you write an application to use Secrets Manager to retrieve and use a secret, you only need to grant that application a very limited set of permissions\. The only permission required is typically the action that allows retrieval of the encrypted secret value with the credentials\. You typically would also specify the Amazon Resource Name \(ARN\) of the secret to restrict access to only that one secret\.

```
{
    "Version": "2012-10-17",
    "Statement": {
        "Effect": "Allow",
        "Action": "secretsmanager:GetSecretValue",
        "Resource": "<arn-of-the-secret-the-app-needs-to-access>"
    }
}
```

For a list of all the permissions that are available to assign in an IAM policy, see [Actions, Resources, and Context Keys You Can Use in an IAM Policy or Secret Policy for AWS Secrets Manager](reference_iam-permissions.md)\.

## Limiting Access to Specific Actions<a name="permissions_grant-limited-actions"></a>

If you want to grant limited permissions instead of full permissions, you can create a policy that lists individual permissions that you want to allow in the `Action` element of the IAM permissions policy\. As shown in the following example, you can use wildcard \(\*\) characters to grant only the `Describe*`, `Get*`, and `List*` permissions, which essentially provides read\-only access to your secrets:

```
{
    "Version": "2012-10-17",
    "Statement": {
        "Effect": "Allow",
        "Action": [
            "secretsmanager:Describe*",
            "secretsmanager:Get*",
            "secretsmanager:List*" 
        ],
        "Resource": "*"
    }
}
```

For a list of all the permissions that are available to assign in an IAM policy, see [Actions, Resources, and Context Keys You Can Use in an IAM Policy or Secret Policy for AWS Secrets Manager](reference_iam-permissions.md)\.

## Limiting Access to Specific Secrets<a name="permissions_grant-limited-resources"></a>

In addition to restricting access to specific actions, you also can restrict access to specific secrets in your account\. The `Resource` elements in the previous examples all specify the wildcard character \("\*"\), which means "any resource that this action can interact with"\. Instead, you can replace the "\*" with the ARN of specific secrets that you want to allow access to\. 

**Example Example: Granting permissions to a single secret by name**  
The first statement of the following policy grants the user read access to the metadata about all of the secrets in the account\. However, the second statement allows the user to perform any Secrets Manager actions on only a single, specified secret by name—or on any secret that begins with the string "`another_secret_name-`" followed by exactly 6 characters:  

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "secretsmanager:DescribeSecret",
                "secretsmanager:List*"
            ],
            "Resource": "*"
        },
        {
            "Effect": "Allow",
            "Action": "secretsmanager:*",
            "Resource": [
                "arn:aws:secretsmanager:<region>:<account-id-number>:secret:a_specific_secret_name-a1b2c3",
                "arn:aws:secretsmanager:<region>:<account-id-number>:secret:another_secret_name-??????"
            ]
        }
    ]
}
```
Using '??????' as a wildcard to match the 6 random characters that are assigned by Secrets Manager avoids a problem that occurs if you use the '\*' wildcard instead\. If you use the syntax "`another_secret_name-*`", it matches not just the intended secret with the 6 random characters, but it also matches "`another_secret_name-<anything-here>a1b2c3`"\. Using the '??????' syntax enables you to securely grant permissions to a secret that doesn't yet exist\. This is because you can predict all of the parts of the ARN except those 6 random characters\. Be aware, however, that if you delete the secret and recreate it with the same name, the user automatically receives permission to the new secret, even though the 6 characters will be different\.  
You get the ARN for the secret from the Secrets Manager console \(on the **Details** page for a secret\), or by calling the `List*` APIs\. The user or group that you apply this policy to can perform any action \(`"secretsmanager:*"`\) on only the two secrets identified by the ARN in the example\.   
If you don't care about the region or account that owns a secret, you must specify a wildcard character \* \(not an empty field\) for the region and account ID number fields of the ARN\.

For more information about the ARNs for various resources, see [Resources That You Can Reference in an IAM Policy or Secret Policy](reference_iam-permissions.md#iam-resources)\. 

## Limiting Access to Secrets That Have Specific Staging Labels or Tags<a name="permissions_grant-limited-condition"></a>

In any of the previous examples, you called out actions, resources, and principals explicitly by name or ARN only\. You can also refine access to include only those secrets that have metadata with a certain tag key and value, or a secret that has a specific label\.

**Example: Granting permission to a secret with metadata that has a certain tag key and value**  
The following policy, when it's attached to a user, group, or role, allows the user to run `GetSecret` on any secret in the current account whose metadata contains a tag with the key "ServerName" and the value "ServerABC"\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": "secretsmanager:DescribeSecret",
            "Resource": "*",
            "Condition": { "StringEquals": { "secretsmanager:ResourceTag/ServerName": "ServerABC" } }
        }
    ]
}
```

**Example: Granting permission to the version of the secret that has a certain staging label**  
The following policy, when it's attached to a user, group, or role, allows the user to run `GetSecret` on any secret with a name that begins with `Prod`—and only for the version that has the staging label `AWSCURRENT` attached\.

```
{
    "Policy": {
        "Version": "2012-10-17",
        "Statement": [
            {
                "Effect": "Allow",
                "Action": "secretsmanager:GetSecret",
                "Resource": "arn:aws:secretsmanager:*:*:secret:Prod*",
                "Condition": { "ForAnyValue:StringEquals": { "secretsmanager:VersionStage": "AWSCURRENT" } }
            }
        ]
    }
}
```

Because a version of a secret can have multiple staging labels attached, you need to use the [IAM policy language's set operators](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_multi-value-conditions.html) to compare them\. In the previous example, `ForAnyValue:StringLike` says that if any one of the staging labels attached to the version being evaluated matches the string `AWSCURRENT`, then the statement matches, and the `Effect` is applied\.

## Granting a Rotation Function Permission to Access a Separate Master Secret<a name="permissions-grant-rotation-role-access-to-master-secret"></a>

When you create a Lambda rotation function using the provided AWS Serverless Application Repository template, either by using the console or by using AWS CLI commands, there's a default policy attached to function's role that controls what the function can do\. By default, this policy grants access *only* to secrets that have this Lambda function configured to be the secret's rotation function\.

If the credentials in the secret don't have permission to change their own password on the secured database or service, then you need to use a separate set of credentials with elevated permissions \(a *superuser*\) that can change this secret's credentials during rotation\. These superuser credentials are stored in a separate "master" secret\. Then, when you rotate your secret, the Lambda rotation function signs in to the database or service with the master credentials to change or update the secret's credentials\. If you choose to implement this strategy, then you must add an additional statement to the role policy attached to the function that grants access to this master secret, in addition to the main secret\.

If you use the console to configure a rotation with a strategy that uses a master secret, then you can select the master secret when you configure rotation for the secret\. 

**Important**  
You must have the **GetSecretValue** permission for the master secret to select it in the console\.

After you finish configuring rotation in the console, you must manually perform the following steps to grant the Lambda function access to the master secret\.

**To grant a Lambda rotation function access to a master secret**  
Follow the steps in one of the following tabs:

------
#### [ Using the console ]

1. When you finish creating a secret with rotation enabled or editing your secret to enable rotation, the console displays a message similar to the following:

   ```
   Your secret MyNewSecret has been successfully stored [and secret rotation is enabled].
   To finish configuring rotation, you need to grant the role MyLambdaFunctionRole permission to retrieve the secret <ARN of master secret>.
   ```

1. Copy the ARN of the master secret in the message to your clipboard\. You will paste it in a later step\.

1. The role name in the preceding message is a link to the IAM console, and navigates directly to that role for you\. Choose that link\.

1. On the **Permissions** tab, there can be one or two inline policies\. One is named `SecretsManager<Name of Template>0`\. It contains the EC2\-related permissions that are required when both the rotation function and your secured service are running in a VPC, and aren't directly accessible from the internet\. The other is named `SecretsManager<Name of Template>1`\. It contains the permissions that enable the rotation function to call Secrets Manager operations\. Open that policy \(the one ending with "1"\) by choosing the expand arrow to the left of the policy and examining its permissions\.

1. Choose **Edit policy**, and then perform the steps in one of the following tabs:

------
#### [ Using the IAM Visual Editor ]

   On the **Visual editor** tab, choose **Add additional permissions**, and then set the following values:
   + For **Service**, choose **Secrets Manager**\.
   + For **Actions**, choose **GetSecretValue**\.
   + For **Resources**, choose **Add ARN** next to the **secret** resource type entry\.
   + In the **Add ARN\(s\)** dialog box, paste the ARN of the master secret that you copied previously\.

------
#### [ Using the JSON editor ]

   On the **JSON** tab, examine the top of the script\. Between lines 3 \(`"Statement": [`\) and line 4 \( `{` \), enter the following lines:

   ```
           {
               "Action": "secretsmanager:GetSecretValue",
               "Resource": "arn:aws:secretsmanager:region:123456789012:secret:MyDatabaseMasterSecret",
               "Effect": "Allow"
           },
   ```

------

1. When you're done editing the policy, choose **Review policy**, and then **Save changes**\.

1. You can now close the IAM console and return to the Secrets Manager console\.

------