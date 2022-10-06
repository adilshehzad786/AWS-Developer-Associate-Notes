# SNS

- direct integration
![3136F4BC-C5AD-4832-B81C-4121EC29717C](https://user-images.githubusercontent.com/53600644/194388828-7d97ff2b-7c4c-473f-984f-93349087571a.jpeg)

  
- Pub / Sub
  ![984354AD-2A53-4CA8-AB45-4758A02A8640](https://user-images.githubusercontent.com/53600644/194388862-7546a47a-9aa1-4642-bb94-e98c23eac7f1.jpeg)


- "event producer" only sends message to one SNS topic
  
- each subscriber to the topic will get all the messages (new feature to filter messages)
  
- up to 10,000,000 subscriptions per topic
  
- 100,000 topics limit
  
- subscribers can be:
  
  - SQS
  - HTTP/HTTPS (with delivery retries - how many times)
  - Lambda
  - Emails
  - SMS messages
  - Mobile Notifications
- integrate with other AWS services:
  
  - CloudWatch (for alarms)
  - ASG notifications
  - S3 (on bucket event)
  - CloudFormation (state change ⇒ failed to build)
  - ...
- Topic Publish (within AWS server - using the SDK)
  
  - create a topic
  - create a subscription
  - publish to the topic
- direct publish (for mobile apps SDK)
  
  - create a platform app
  - create a platform endpoint
  - publish to platform endpoint

## SNS + SQS: Fan Out

- push once in SNS, receive in many SQS
- No data loss
- Ability to add receivers of data later (when number of messages increase, can add consumer to handle)
- SQS allows for delayed processing
- SQS allows for retries of work
