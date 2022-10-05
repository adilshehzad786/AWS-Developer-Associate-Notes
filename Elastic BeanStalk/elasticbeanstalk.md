# Beanstalk

## Phases of Release Lifecycle

### Source

During the source phase, developers check changes into a source code repository. Many teams requires peer feedback on code changes before delivery code to target environment.

### Build

During the build process, application source code is built, quality of the code is tested on the build machine.

### Test

The test phase is to perform a test that cannot be done during the build phase and that requires the software to be deployed to the target environment. These test includes testing integration with other live systems, load testing, UI testing, and penetrations testing.

### Deploy

In the deployment phase, the code is deployed to the target environment.

### Monitor

Monitor phases, and check the application to detect unusual activities and errors quickly.

## Deploying Highly Available and Scalable Applications

Load balancing is a integral part to directing and managing traffic among your instances.

1. **Application Load Balancer** : This load balancer operates at level (Layer 7 ) to route HTTP/HTTPS traffic to its targets.
2. **Network Load Balancer** : This loadbalancer operates at the connection level (Layer 4) t route TCP traffic to targe .
3. **Classic Load balancer :** This loadbalancer operates at both request level and the connection level.

### Deploy and Maintain Services

1. **AWS Beanstalk -** With AWS BeanStalk you do not need to worry about managing the infrastructure for your application. You deploy your application such as Python application and elastic beanstalk takes care of scaling and managing it.
2. **AWS OpsWork-** AWS OpsWorks is a configuration and deployment management tool for your chef or puppet resources stack.
3. **AWS Cloudformation-** is infrastructure as code. The service helps you model and set up AWS resources so that you can spend less time managing them.

## Elastic BeanStalk Overview

- managed service
  - instance configuration / OS is handled by beanstalk
  - deployment strategy is configurable but performed by BeanStalk
- just the application code is the responsibility of the developer
- three architecture models:
  - single instance deployment: good for dev
  - LB + ASG: great for production or pre-production web applications
  - ASG only: great for non-web apps in production (workers, etc...)
- include 3 components:
  - application
  - application version: each deployment gets assigned a version
  - environment name (dev, test, prod...): free naming
- deploy application version to environments and can promote application versions to the next environment
- rollback to previous application version
- full control over lifecycle of environments

## Deployment Strategy

### All at once

- fastest deployment
- application has downtime
- great for quick iterations in development environment
- no additional cost

![0905BCEB-5ADF-41CD-95F6-F58C36C53549](https://user-images.githubusercontent.com/53600644/194135776-7becdd8b-d1a6-4565-9f0d-6544161dd6d7.jpeg)


### Rolling

- application is running below capacity
- can set the bucket size
- application is running both versions simultaneously
- no additional cost
- long deployment
![C33A92C5-DFFF-441B-B15C-6F69FA061ABC](https://user-images.githubusercontent.com/53600644/194135836-c670a67e-3758-42eb-bb2a-b8693acad46c.jpeg)



### Rolling with additional batches

- application is running at capacity
- can set the bucket size
- application is running both versions simultaneously
- small additional cost
- additional batch is removed at the end of the deployment
- longer deployment
- good for prod

![EB49BCFD-1629-49EB-BA0E-B127A2A4A817](https://user-images.githubusercontent.com/53600644/194135861-b40674bb-da8b-46da-aff9-d5940edd8f60.jpeg)


### Immutable

- zero downtime
- new code is deployed to new instances on a temporary ASG
- high cost, double capacity
- longest deployment
- quick rollback in case of failures (just terminate new ASG)
- great for prod

![3D42B2F6-31E0-4F2D-A39A-9E0C86F201FF](https://user-images.githubusercontent.com/53600644/194135884-cf1d27ec-f40f-4072-9f40-672d0448a732.jpeg)


### Blue / Green

- not a "direct feature" of Beanstalk
- zero downtime and release facility
- create a new "stage" environment and deploy v2 there
- the new environment (green) can be validated independently and roll back if issues
- Route53 can be setup using weighted policies to redirect a little bit of traffic to the stage environment
- Using Beanstalk, "swap URLs" when done with the environment test

![13EA311F-FF99-429F-9C57-903ED3723699](https://user-images.githubusercontent.com/53600644/194135918-9347f106-c0ef-4053-96a5-263e263be5ef.jpeg)

### Comparison Table 

|Method                       |Impact of failed deployment                                                                       |Deploy time|Zero downtime|No DNS change|Rollback process       |Code deployed to                   |
|-----------------------------|--------------------------------------------------------------------------------------------------|-----------|-------------|-------------|-----------------------|-----------------------------------|
|All at once                  |Downtime                                                                                          |🕒         |☓            |✓            |Manual Redeploy        |Existing instances                 |
|Rolling                      |Single batch out of service; any successful batches before failure running new application version|🕒🕒☨      |✓            |✓            |Manual Redeploy        |Existing instances                 |
|Rolling with additional batch|Minimal if first batch fails; otherwise, similar to Rolling                                       |🕒🕒🕒☨    |✓            |✓            |Manual Redeploy        |Redeploy	New and existing instances|
|Immutable                    |Minimal                                                                                           |🕒🕒🕒🕒   |✓            |✓            |Terminate New Instances|New instances                      |
|Blue/green                   |Minimal                                                                                           |🕒🕒🕒🕒   |✓            |☓            |Swap URL               |New instances                      |


## Elastic Beanstalk Extensions

- all the parameters set in the UI can be configured with code using files
- requirements:
  - in the `.ebextensions/` directory in the root of source code
  - YAML/JSON format
  - `*.config` extensions
  - able to modify some default settings using: option_settings
  - ability to add resources such as RDS, ElastiCache, DynamoDB...
- Resources managed by `.ebextensions` get deleted if the environment goes away

## Beanstalk Lifecycle Policy

- Beanstalk can store at most 1000 application versions
- if not remove old versions ⇒ can't deploy anymore
- 2 types of lifecycle policy:
  - based on time (old versions are removed)
  - based on space (when you have too many versions)
- versions that are currently used won't be deleted
- option not to delete the source bundle in S3 to prevent data loss

## Web server vs Worker Environment

- application performs tasks that are long to complete, offload ⇒ use **worker environment**
- decoupling application into 1 tiers is common
- Eg: processing a video, generating a zip file...
- can define periodic tasks in a file cron.yaml

![Untitled](https://user-images.githubusercontent.com/53600644/194136196-d9a8d4fc-519e-48a8-859e-311f9f3c9872.png)


## RDS with Elastic Beanstalk

- RDS provisioned with Beanstalk is great for dev/test
- use separate RDS for production
- migrate from RDS coupled with EB to standalone RDS:
  - take RDS db snapshot
  - enable deletion protection in RDS
  - create a new environment without an RDS, point to existing old RDS
  - perform blue/green deployment and swap new and old environments
  - terminate the old environment (RDS won't get deleted thanks to protection)
  - delete CloudFormation stack (will be in DELETE_FAILED state)

## Tips

- under the hood, Beanstalk relies on CloudFormation
- package code as zip file ⇒ zip file is uploaded to each EC2 machine ⇒ each EC2 machine resolves dependencies ⇒ slow
  - optimization: package dependencies with source code to improve deployment performance and speed
- Beanstalk with HTTPS
  - load the SSL certificate onto the Load Balancer
  - can be done from Console (EB console, load balancer configuration)
  - can be done from the code `.ebextension/securelistener-alb.config`
  - SSL certificate can be provisioned using ACM (AWS certificate manager) or CLI
  - Must configure a security group rule to allow incoming port 443 (HTTPS port)
- Beanstalk redirect HTTP to HTTPS
  - configure instances to redirect HTTP to HTTPS
  - or configure the Application Load Balancer (only) with a rule
  - make sure health checks are not redirected
