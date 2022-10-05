# Developer Tools

## Continuous Delivery with AWS Code pipeline

The AWS Code service lay the foundation to deploy different parts of an enterprise starting from a source repository. You can start with AWS Codepipeline to create a continuous integration/continuous deployment pipeline (CI/CD) that integerates various sources , tests, deployments or other components. AWS Code pipeline implements AWS CodeCommit as a source in that it acts as the intilization point of your deployment process. AWS CodeBuild allow you to pull code and packages from various sources to create publishable build artifacts. AWS code Deploy allow you to deploy compiled artifacts to infrastructure in your enviornment. AWS Code pipeline is not limited to deploying application , it can be use for provision , configure and manage infrastructure.

![Untitled](file://C:\Users\ashehzad\Downloads\Export-2486ab57-c791-455b-98fb-70fe1938108b\Developer%20Tools%209316c3d9da4b4ffbb613501c2e8260e2\Untitled.png)

## Overview

- codecommit: source code control
- codebuild: CI
- codedeploy: CD
- codepipeline: combines source control, build, and deployment

![Developer%20Tools%209316c3d9da4b4ffbb613501c2e8260e2/Untitled%201png](file://C:\Users\ashehzad\Downloads\Export-2486ab57-c791-455b-98fb-70fe1938108b\Developer%20Tools%209316c3d9da4b4ffbb613501c2e8260e2\Untitled%201.png)

## CodeCommit

### Security

- Authentication in Git:
  - SSH Keys: can configure SSH keys in IAM Console
  - HTTPS: do with AWS CLI Authentication helper or Generating HTTPS credentials
  - MFA: can be enable
- Authorization in Git:
  - IAM Policies manage user / roles
- Encryption:
  - automatically encrypt repositories at rest using KMS
  - encrypted in transit (can only use HTTPS or SSH)
- Cross Account access:
  - never share SSH keys
  - never share AWS credentials
  - use IAM Role and use AWS STS (with AssumeRole API)

### Notifications

- can trigger notifications in CodeCommit using AWS SNS, Lambda, CloudWatch Event Rules
- use cases for SNS, Lambda
  - deletion of branches
  - pushes that happens in master branch
  - notify external Build System
  - trigger Lambda to perform codebase analysis
- use cases for CloudWatch Event Rules
  - pull request updates (created/upload/deleted/commented)
  - commit comment events
  - CloudWatch Event Rules goes into an SNS topic

### CodeCommit vs GitHub:

- both are git repositories
- both support code reivew (pull request)
- both can be integrated with AWS CodeBuild
- both support HTTPS, SSH
- security
  - github: github users
  - codecommit: AWS IAM users + roles
- hosted
  - github: hosted by github
  - github enterprise: selft hosted on client servers
  - codecommit: managed + hosted by AWS
- UI
  - github UI is fully featured
  - codecommit UI: minimal

## CodeBuild

- build instructions can be defined in code by `buildspec.yml` file - must be at the root of the code
  - define environment variables:
    - plaintext variables
    - secure secrets: use SSM Parameter store
  - phases (command to run):
    - install: install dependencies
    - pre build: final commands to execute before build
    - build: actual build commands
    - post build: finishing touches (eg: zip output)
  - artifacts: what to upload to S3 (encrypted with KMS)
  - cache: Files to cache (eg: dependencies) to S3 for future build speedup

![Developer%20Tools%209316c3d9da4b4ffbb613501c2e8260e2/Untitled%202png](file://C:\Users\ashehzad\Downloads\Export-2486ab57-c791-455b-98fb-70fe1938108b\Developer%20Tools%209316c3d9da4b4ffbb613501c2e8260e2\Untitled%202.png)

## CodeDeploy

- deploy application automatically to many EC2 instances
- each EC2 instances (or on-premise) must be running the CodeDeploy Agent
- the agent is continuously polling AWS CodeDeploy for work to do
- CodeDeploy sends `appspec.yml` file
- application is pulled from Github or S3
- EC2 will run the deployment instructions
- CodeDeploy Agent will report of success/failure of deployment on the instance

![Developer%20Tools%209316c3d9da4b4ffbb613501c2e8260e2/Untitled%203png](file://C:\Users\ashehzad\Downloads\Export-2486ab57-c791-455b-98fb-70fe1938108b\Developer%20Tools%209316c3d9da4b4ffbb613501c2e8260e2\Untitled%203.png)

- EC2 will be grouped by deployment group (dev/test/prod)
- Blue / Green only works with EC2 instances (not on premise)
- can deploy to Lambda
- `appspec.yml`
  - file sections: how to source and copy from S3/Github to filesystem
  - hooks: set of instructions to do to deploy the new version (hooks can have timeout). The order is:
    - ApplicationStop
    - DownloadBundle
    - BeforeInstall
    - AfterInstall
    - ApplicationStart
    - ValidateService: really important
- configs:
  - one a time: one instance at a time, one instance fails ⇒ deployment stops
  - half at a time: 50%
  - all at once: quick but no healthu host, downtime ⇒ good for dev
  - custom: min healthy host = a%
- failures:
  - instances stay in "failed state"
  - **new deployments will first be deployed to "failed state" instances**
  - to rollback: redeploy old deployment or enable automated rollback for failures
- deployment targets:
  - **set of EC2 instances with tags**
  - **directly to an ASG**
  - mix of ASG/Tags
  - customization in scripts with DEPLOYMENT_GROUP_NAME env variables

## CodePipeline

- made of stages:
  
  - each stage can have sequential actions and / or parallel actions
  - Stages examples: Source/Build/Deploy/Review, Build/Test/Deploy/Load Test
  - manual approval can be defined at any stage
- each stage can create artifacts
  
  - artifacts are passed and stored in Amazon S3 and passed on to the next stage
    
    ![Developer%20Tools%209316c3d9da4b4ffbb613501c2e8260e2/Untitled%204png](file://C:\Users\ashehzad\Downloads\Export-2486ab57-c791-455b-98fb-70fe1938108b\Developer%20Tools%209316c3d9da4b4ffbb613501c2e8260e2\Untitled%204.png)
    
- each stage can have multiple action group
  
- if codepipeline fails a stage, it stops and show information in the console
  
- can create SNS notifications for
  
  - failed pipelines
  - cancelled stages
  - ...
- if codepipeline can't perform an action, make sure its' IAM Role have enough permissions
  

## CodeStar

- integrate solution that regroups: Github, CodeCommit, CodeBuild, CodeDeploy, CloudFormation, CodePipeline, CloudWatch
- one dashboard to view all components
- quickly create CICD-ready projects for EC2, Lambda, Beanstalk