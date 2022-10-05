# KMS

## Centralized Key Management System

AWS KMS provides you with a centralized view of your encryption keys. you can create a customer master key (CMK) to control access to your data encryptions data and encrypt an encrypt your data. AWS Key Management System Use AES-256 Bit mode to encrypt and secure your data.

### Custom Key Store

You can create your own custom key store in a CloudHSM cluster that you control enabling you to store your AWS KMS keys in a single tenant environment instead of the default multi-tenant environment of AWS KSM.

### CloudHSM

AWS CloudHSM offer third party, validated FIPS 140-2 level three hardware security modules in the AWS Cloud. The hardware security module is a computing device that provide a dedicated infrastructure to support cryptographic operations.

## Things you Should know!

- never store your secrets key in plaintext ⇒ can store encrypted secret key in code / environment variables
- KMS can only help in encrypting up to 4KB of data per call
- if data > 4KB, use envelope encryption (client side encrypt)
- give access to KMS:
  - make sure the Key Policy allows the user
  - make sure the IAM Policy allows call to KMS API
- manage the keys & policies:
  - create
  - rotation policies
  - disable
  - enable
- can audit key usage (using CloudTrail)
- 2 types of CMK:
  - AWS Managed Service Default CMK: free
  - User Keys created in KMS: 1$/month
  - User Keys imported (must be 256-bit symmetric key): 1$/month
- pay for API call to KMS (0.03$ / 10000 calls)

## AWS Encryption SDK

- encrypt over 4KB using KMS
- different from the S3 Encryption SDK
- Encryption SDK = Envelope Encryption = GenerateDataKey API

## AWS Parameter Store

- Systems Manager / Parameter Store UI
- secure storage for configuration and secrets
- encrypt using KMS
- serverless, scalable, durable, easy SDK, free
- version tracking
- manage configuration (who can see, decrypt...) by path & IAM
- notifications with CloudWatch Events
- integration with CloudFormation