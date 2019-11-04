# Creating a Basic Secret<a name="manage_create-basic-secret"></a>

AWS Secrets Manager enables you to store basic secrets with a minimum of effort\. A "basic" secret is one with a minimum of metadata and a single encrypted secret value\. The one version that's stored in the secret is automatically labeled `AWSCURRENT`\. <a name="proc-create"></a>

**To create a basic secret**  
Follow the steps under one of the following tabs:

------
#### [ Using the console ]<a name="proc-create-console"></a>

**Minimum permissions**  
To create a secret in the console, you must have these permissions:  
The permissions granted by the **SecretsManagerReadWrite** AWS managed policy\.
The permissions granted by the **IAMFullAccess** AWS managed policy – required only if you need to enable rotation for the secret\.
`kms:CreateKey` – required only if you need to ask Secrets Manager to create a custom AWS KMS customer master key \(CMK\)\. 
`kms:Encrypt` – required only if you use a custom AWS KMS key to encrypt your secret instead of the default Secrets Manager CMK for your account\. You don't need this permission to use the account's default AWS managed CMK for Secrets Manager\.
`kms:Decrypt` – required only if you use a custom AWS KMS key to encrypt your secret instead of the default Secrets Manager CMK for your account\. You don't need this permission to use the account's default AWS managed CMK for Secrets Manager\.
`kms:GenerateDataKey` – required only if you use a custom AWS KMS key to encrypt your secret instead of the default Secrets Manager CMK for your account\. You don't need this permission to use the account's default AWS managed CMK for Secrets Manager\.

1. Sign in to the AWS Secrets Manager console at [https://console\.aws\.amazon\.com/secretsmanager/](https://console.aws.amazon.com/secretsmanager/)\.

1. Choose **Store a new secret**\.

1. In the **Select secret type** section, specify the kind of secret that you want to create by choosing one of the following options\. Then supply the required information\.

------
#### [ Amazon RDS ]<a name="rds-creds"></a>

   This secret is for one of the [supported database services](intro.md#full-rotation-support) for which Secrets Manager provides full rotation support with a preconfigured Lambda rotation function\. You need specify only the authentication credentials because Secrets Manager learns everything else it needs by querying the database instance\.

   1. Type the user name and password that allow access to the database\. Choose a user that has only the permissions that are required by the customer who will access this secret\.

   1. Choose the AWS KMS encryption key that you want to use to encrypt the protected text in the secret\. If you don't choose one, Secrets Manager checks to see if there's a default key for the account, and uses it if it exists\. If a default key doesn't exist, Secrets Manager creates one for you automatically\. You can also choose **Add new key** to create a custom CMK specifically for this secret\. To create your own AWS KMS CMK, you must have permissions to create CMKs in your account\. 

   1. Choose the database instance from the list\. Secrets Manager retrieves the connection details about the database by querying the chosen instance\.

------
#### [ Amazon Redshift ]<a name="redshift-creds"></a>

   This secret is for an Amazon Redshift cluster\. You need specify only the authentication credentials because Secrets Manager learns everything else it needs by querying the database instance\.

   1. Type the user name and password that allow access to the database\. 

   1. Choose the AWS KMS encryption key that you want to use to encrypt the protected text in the secret\. If you don't choose one, Secrets Manager checks to see if there's a default key for the account, and uses it if it exists\. If a default key doesn't exist, Secrets Manager creates one for you automatically\. You can also choose **Add new key** to create a custom CMK specifically for this secret\. To create your own AWS KMS CMK, you must have permissions to create CMKs in your account\. 

   1. Choose the correct database engine\. 

   1. Specify the connection details by typing the database server's IP address, database name, and TCP port number\. 

------
#### [ DocumentDB database ]<a name="documentdb-creds"></a>

   This secret is for a DocumentDB database\. You need specify only the authentication credentials because Secrets Manager learns everything else it needs by querying the database instance\.

   1. Type the user name and password that allow access to the database\. 

   1. Choose the AWS KMS encryption key that you want to use to encrypt the protected text in the secret\. If you don't choose one, Secrets Manager checks to see if there's a default key for the account, and uses it if it exists\. If a default key doesn't exist, Secrets Manager creates one for you automatically\. You can also choose **Add new key** to create a custom CMK specifically for this secret\. To create your own AWS KMS CMK, you must have permissions to create CMKs in your account\. 

   1. Choose the correct database engine\. 

   1. Specify the connection details by typing the database server's IP address, database name, and TCP port number\.

------
#### [ Other databases ]<a name="nonrds-creds"></a>

   This secret is for a database service that Secrets Manager knows about and supports\. However, Secrets Manager needs you to provide additional information about the database\. To rotate this secret, you must write a custom Lambda rotation function that can parse the secret and interact with the service to rotate the secret on your behalf\. 

   1. Type the user name and password that allow access to the database that you want to protect in this secret\.

   1. Choose the AWS KMS encryption key that you want to use to encrypt the protected text in the secret\. If you don't choose one, Secrets Manager checks to see if there's a default key for the account, and uses it if it exists\. If a default key doesn't exist, Secrets Manager creates one for you automatically\. You can also choose **Add new key** to create a custom CMK specifically for this secret\. To create your own AWS KMS CMK, you must have permissions to create CMKs in your account\.

   1. Choose the type of database engine that runs your database\.

   1. Specify the connection details by typing the database server's IP address, database name, and TCP port number\.

------
#### [ Other type of secret ]<a name="other-creds"></a>

   This secret is for a database or service that Secrets Manager doesn't natively know about\. You must supply the structure and details of your secret\. To rotate this secret, you must write a custom Lambda rotation function that can parse the secret and interact with the service to rotate the secret on your behalf\. 

   1. Specify the details of your custom secret as **Key** and **Value** pairs\. For example, you can specify a key of UserName, and then supply the appropriate user name as its value\. Add a second key with the name of Password and the password text as its value\. You could also add entries for `Database name`, `Server address`, `TCP port`, and so on\. You can add as many pairs as you need to store the information you require\.

      Alternatively, you can choose the **Plaintext** tab and enter the secret value in any way you like\. 

   1. Choose the AWS KMS encryption key that you want to use to encrypt the protected text in the secret\. If you don't choose one, Secrets Manager checks to see if there's a default key for the account, and uses it if it exists\. If a default key doesn't exist, Secrets Manager creates one for you automatically\. You can also choose **Add new key** to create a custom CMK specifically for this secret\. To create your own AWS KMS CMK, you must have permissions to create CMKs in your account\.

------

1. For **Secret name**, type an optional path and name, such as **production/MyAwesomeAppSecret** or **development/TestSecret**\. Notice that the use of the slash character enables you to structure your secrets into a hierarchy, such as grouping by the deployment environment, which is useful for organizing and managing your secrets at scale. You can optionally add a description to help you remember the purpose of this secret later on\.

   The secret name must be ASCII letters, digits, or any of the following characters: /\_\+=\.@\-

1. \(Optional\) In the **Tags** section, you can add one or more tags to your secret\. A tag consists of a key and a value that you define\. Tags assist with managing your AWS resources\. You can create tags that associate resources with your organization's structure, such as Key="Department" and Value="Accounting"\. This can help with cost allocation and tracking\. You can assign tags to group resources together by the application that uses them \(Key="AppName" and Value="HRDatabase"\)\. You can create tags for almost any purpose\. Each resource, like a secret, can have several tags attached\. For more information, see [AWS Tagging Strategies](https://aws.amazon.com/answers/account-management/aws-tagging-strategies/) on the *AWS Answers* website\.
**Important**  
Don't store sensitive information about a secret in its tags\. Store sensitive information only in the secret value \(the `SecretString` or `SecretBinary` fields\) of the secret where the information is protected with encryption\.

1. After you complete the **Name**, **Description**, and any **Tags**, choose **Next**\.

1. \(Optional\) At this point, you can configure rotation for your secret\. Because we're working on a "basic" secret without rotation, keep it as **Disable automatic rotation**, and then choose **Next**\.

   For information about how to configure rotation on new or existing secrets, see [Rotating Your AWS Secrets Manager Secrets](rotating-secrets.md)\.

1. Review your settings, and then choose **Store secret** to save everything that you entered as a new secret in Secrets Manager\.

------
#### [ Using the AWS CLI or AWS SDK operations ]<a name="proc-create-api"></a>

You can use the following commands to create a basic secret in Secrets Manager:
+ **API/SDK:** [https://docs.aws.amazon.com/secretsmanager/latest/apireference/API_CreateSecret.html](https://docs.aws.amazon.com/secretsmanager/latest/apireference/API_CreateSecret.html)
+ **AWS CLI:** [https://docs.aws.amazon.com/cli/latest/reference/secretsmanager/create-secret.html](https://docs.aws.amazon.com/cli/latest/reference/secretsmanager/create-secret.html)

**Example**  
Here's an example AWS CLI command that performs the equivalent of the console\-based secret creation on the other tab\. This command assumes that you've placed your secret, such as this example JSON text structure `{"username":"anika","password":"aDM4N3*!8TT"}`, in a file named `mycreds.json`\.  

```
$ aws secretsmanager create-secret --name production/MyAwesomeAppSecret --secret-string file://mycreds.json
{
    "SecretARN": "arn:aws:secretsmanager:region:accountid:secret:production/MyAwesomeAppSecret-AbCdEf",
    "SecretName": "production/MyAwesomeAppSecret",
    "SecretVersionId": "EXAMPLE1-90ab-cdef-fedc-ba987EXAMPLE"
}
```

**Important**  
You can create a basic secret using whatever format for `SecretString` that you want\. For example, you could use a simple JSON key\-value pair\. For example, `{"username":"someuser", "password":"securepassword"}` However, if you later want to enable rotation for this secret then you must use the specific structure expected by the rotation function that you use with this secret\. For the details of what each rotation function requires to work with the secret value, see the **Expected SecretString Value** entry under the relevant rotation function at [AWS Templates You Can Use to Create Lambda Rotation Functions ](reference_available-rotation-templates.md)\.

The `ClientRequestToken` parameter isn't required because we're using the AWS CLI, which automatically generates and supplies one for us\. We also don't need the `KmsKeyId` parameter because we're using the default Secrets Manager CMK for the account\. If you're using `SecretString`, you can't use `SecretBinary`\. `SecretType` is reserved for use by the Secrets Manager console\.

In a working environment, where your customers use an app that uses the secret to access a database, you might still need to grant permissions to the IAM user or role that the app uses to access the secret\. You can do this by attaching a resource\-based policy directly to the secret, and listing the user or role in the `Principal` element\. Or you can attach a policy to the user or role that identifies that identifies the secret in the `Resource` element\.

------