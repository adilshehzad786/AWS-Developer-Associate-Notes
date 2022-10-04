# API Credentials and AWS Identity and Access Management

## AWS Software Development Kits

AWS SDKs are available in many popular languages such as Java, .NET, JavaScript, PHP, Python, Ruby , Go, and C++. The Python SDK for AWS is called AWS SDK for Python (Boto).It allows developers to access AWS from JavaScript code that runs directly in the browser, and it includes access to AWS components like Amazon S3, Amazon SNS, Amazon SQS, DynamoDB, and more.

## AWS CLI

The AWS CLI is an open-source tool built on top of the AWS SDK for Python(Boto) that provides commands for interacting with AWS Services. With Minimal configurations, you can start using all of the functionality provided by AWS Console from your favorite terminal program.

- use aws configure to set your configurations
- use `—dry-run` to check cli command have permission
- `aws sts decode-authorization-message --encoded-message <err_message>` to show entire message
- exponential backoff
  - any API that fails because of too many calls needs to be retried with exponential backoff
  - apply to rate limited API
  - also apply to SDK call api
- `aws configure --profile <profile_name>` config multiple aws profiles
  - `aws s3 ls —-profile <profile_name>` use aws cli with specific profile
- Can create Access Keys/ Secret Access Keys to allow IAM users (or service accounts) to be used with AWS CLI or API calls

## Working With Regions

Each AWS region is located in a separate geographic area and maintains its own, isolated copies of AWS Services. Each AWS Regions contain multiple data centers, grouped together to form Availability Zones. Regions are composed of multiple Availability zones, which allows AWS to provide highly available services in a way that differentiates them from traditional architecture with single or multiple data centers. Availability zones are physically separated from each other and designed to operate independently in case of a fault or natural disaster.

## Identity Access and Management (IAM)

## Users

- A user can represent a real person who requires access to operate and maintain your AWS Environment.
- A user can be used by an application that requires permissions to access your AWS resources programmatically
- Permissions can be assigned to the user or inherited from a group

 ![User_(1)](https://user-images.githubusercontent.com/53600644/193903172-f657b6db-eb8c-4b1a-a092-8eba574a137e.png)

## Groups

- IAM Groups are objects like user objects
- Groups are not used in the authentication process
- They are used to authorize access through AWS Policies
- IAM Group contains Users and has IAM Policies Associated.
- AWS Has a default maximum limit of hundred groups
- A user can only be associated with 10 groups

![Untitled](https://user-images.githubusercontent.com/53600644/193903219-678f4cbc-28c4-4e24-b066-853a78c1766f.png)


## Roles

IAM Roles allow you to adopt a set of temporary IAM Permissions

There are currently four different types of Roles

1. AWS Service Role
2. AWS Service - Linked Role
3. A Role for Cross Accounts Access
4. A Role for Identity Provider Access

![Untitled 1](https://user-images.githubusercontent.com/53600644/193903285-de101e3d-cec6-4d26-8eb9-155c443c24ed.png)


## Advanced IAM - Authorization Model

- **AWS Managed Policy**
  - maintained by AWS
  - good for power users and administrators
  - updated in case of new services/ new api
- **customer Managed Policy**
  - best practice, re-usable, can be applied to many principals
  - version controlled + rollback, central change management
- **Inline**
  - strict one-to-one relationship between policy and principal
  - policy is deleted if you delete the IAM principal

### AWS Policy Structure

```json
"Version": "2012-10-17",
 "Statement": [{
 "Sid": "AllowDeleteForSpecifiedLexicon",
 "Effect": "Allow",
 "Action": [
 "polly:DeleteLexicon"],
 "Resource": "arn:aws:polly:us-west-2:123456789012:lexicon/awsLexicon"
 }
 ]
}
```

*Effect* –Allow or Deny access to the resource is decided by Effect (Allow/Deny)

*Action* — A set of service-specific parameters (like “iam: CreateUser”).

*Resource* — Resource names (like “arn:aws:s3:::conf-* “)

*Condition* (Optional) — Grant conditions (like “aws: RequestedRegion”: “ap-south-1”)

## Permissions Evaluations Structure
![Untitled 2](https://user-images.githubusercontent.com/53600644/193903420-341e8f9a-65bd-498f-843c-7bc68a8d184b.png)


### IAM Policies & S3 Bucket Policies

- IAM policies are attached to users, roles, groups
- S3 Bucket Policies are attached to buckets

- Example
  
  - IAM Role attached to an EC2 instance, Read Write permission provided to bucket" + no S3 Bucket policy attached
    
    ⇒ EC2 instance can read, write to the bucket
    
  - IAM Role attached to an EC2 instance, RW permission to bucket + S3 Bucket Policy deny to the IAM Role
    
    ⇒ EC2 instance cannot read, write to "bucket"
    
  - IAM Role attached to an EC2 instance, no S3 bucket permission + S3 Bucket Policy attached, RW permission to the IAM Role
    
    ⇒ EC2 instance can read, and write to "bucket"
    
  - IAM Role attached to an EC2 instance, deny S3 bucket permission + S3 Bucket Policy attached, allow RW permission to the IAM Role
    
    ⇒ EC2 instance cannot read, write to "bucket"

![Untitled 3](https://user-images.githubusercontent.com/53600644/193903450-7a395f6c-19a1-4e5e-82f1-11ba5725346e.png)


### Dynamic Policies

- assign each user a `/home/<user>` folder in S3
- create only one dynamic policy with IAM
- use the special policy variable `${aws:username}`

```json
{
    "Sid": "AllowAllS3ActionsInUserFolder",
    "Action": ["s3:*"],
    "Effect": "Allow",
    "Resource": ["arn:aws:s3:::my-company/home/${aws:username}/*"]
}
```

### STS

- Grants users limited and temporary access to AWS resources.
- 3 sources:
  1. Federation (often Active Directory)
    - Uses SAML
    - SSO allows users to log in to AWS Console without assigning IAM credentials
  2. Federation with mobile app
    - Use Facebook/Amazon/Google or other openID provider
  3. Cross account access
    - Lets users from one AWS account access resources in another

![Untitled 4](https://user-images.githubusercontent.com/53600644/193903552-860c46c7-cfbf-471d-be9a-e83be93ebb21.png)


## Things to Know

- IAM is global
- IAM has many predefined "managed policies" by Amazon
- Can integrate users with IAM, so the employee can log in to AWS using company credentials by using **Identity Federation** (Eg: company use Active Directory, SAML standard)
- use MFA (Multi-Factor Authentication)
- give users the minimal amount of permissions they need to perform their job
- 1 IAM User per 1 Physical Person
- 1 IAM Role per Application
- never share IAM credentials
- never write IAM credential in code or commit to git
- only use the ROOT account for initial setup
- never put personal credentials on EC2 ⇒ use IAM roles instead
- on-premise server, the best practice is to call STS to obtain temporary security credentials
- cross-account access
  - define an IAM Role for another account to access
  - define which accounts can access this IAM Role
  - use AWS STS (Security Token Service) to retrieve credentials and impersonate the IAM Role you have access to (`AssumeRole` API)
  - temporary credentials can be valid between 15 minutes to 1 hour

### Resources

* AWS Whitepapers (Introduction to AWS):
[https://aws.amazon.com/whitepapers/](https://aws.amazon.com/whitepapers/)
* AWS Training and Certification:
[https://aws.amazon.com/training](https://aws.amazon.com/training)
* AWS Events and Webinars:
[https://aws.amazon.com/about-aws/events](https://aws.amazon.com/about-aws/events)
