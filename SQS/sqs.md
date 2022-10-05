# SQS

![E3A330F4-9B42-409B-8248-839DC5113426](https://user-images.githubusercontent.com/53600644/194141048-7a374665-13aa-4455-a2a8-2b09cd833c9e.jpeg)


## Application communication

- synchronous communications
  ![0B8C1B7A-0867-446E-A46E-8489681F86CE](https://user-images.githubusercontent.com/53600644/194141101-81dfeb5b-4f42-491b-a80c-dbb15dfdeb0a.jpeg)


  ⇒ can be problematic if there are sudden spikes of traffic
  
- asynchronous / event based
  ![9E25FC29-91BC-49D0-9746-B534E73F0BFF](https://user-images.githubusercontent.com/53600644/194141142-43b3b6f3-0636-4a0c-bd85-6b8dfa32f405.jpeg)

  
  ⇒ scalable
  

## Producing messages

- define body
- add message attributes (metadata - optional)
- delay delivery (optional)
- get back
  - message identifier
  - MD5 hash of body
![C4AE4E3D-C9CE-4CBB-A7E8-29D591156D7B](https://user-images.githubusercontent.com/53600644/194141497-f8611f0e-3812-4269-a20a-f6518ec63ad8.png)


## Consuming messages

- poll message from SQS (maximum 10 messages at a time)
- process the message within the visibility timeout
- delete the message using message ID & receipt handle

![3B962D11-7210-4CD8-9C60-76DF91E522F8](https://user-images.githubusercontent.com/53600644/194141174-5369f4cf-e2af-4b0e-962b-3663347d4db6.jpeg)


### visibility timeout

- when a consumer polls a message from a queue ⇒ the message is invisible to other consumers for a defined period ⇒ **Visibility Timeout**
  - 0 second - 12 hours (default 30 seconds)
  - when consumer fails to process the message, the message is still invisible until **Visibility Timeout** finish ⇒ be carefull to set high Visibility Timeout (eg 15 minutes)
  - if too low (30 senconds) and consumer needs time to process the message (2 minutes) ⇒ another consumer will receive and process the message more than once
- **ChangeMessageVisibility** API to change the visibility
- **DeleteMessage** API to tell SQS the message was successfully processed

### Long Polling

- when a consumer requests message from the queue, it can optionally "wait" for messages to arrive if there are none in the queue
  
  ⇒ decrease the number of API call
  
- wait time between 1 second - 20 second (20 is preferable)
  
- Long Polling > Short Polling
  
- enable at queue level or **WaitTimeSeconds** API
  
- Short polling
  
![38EDDEFD-FFC3-4DA5-96DA-11134B733336](https://user-images.githubusercontent.com/53600644/194141198-1ebf1019-1af2-4fe4-8e45-88b7ef71b43e.jpeg)



- long polling

![B46EC6B8-E519-4F05-BC0A-55B9BF4C8245](https://user-images.githubusercontent.com/53600644/194141224-39dc48f0-1bf4-421e-b70a-a8a39987923e.png)


## Standard Queue

![E6E72E5A-C02B-45F4-83ED-E60EE47F331E](https://user-images.githubusercontent.com/53600644/194141264-726d9981-5703-4685-abc8-b342881379f0.jpeg)


- **unlimited** throughput
- retention 4 days by default, maximum 14 days
- no limit number of messages in the queue
- low latency <10ms
- horizontal scaling in terms of number of consumers
- can have **duplicate** messages (at least once delivery)
- can have **out of order** messages (best-effort ordering)
- limit 256KB per message

### Delay Queue

- delay a message up to 15 minutes (consumers don't see it immediately)
- default is 0 seconds
- can set a default at queue level (per message delay + per queue delay)
- can override the default using the **DelaySeconds** parameter

### Dead Letter Queue

- set threshold of how many times a message can go back to the queue (because of failure process) ⇒ **redrive policy**
- by default DLQ is not enable, messages don't go back the the queue
- after the threshold is exceeded, the message goes into a DLQ

![E6485E57-AEE1-487C-BE0D-89F1AAC5B0D1](https://user-images.githubusercontent.com/53600644/194141283-afff32e9-9932-40a8-95d5-85ca3ac6c648.jpeg)


## FIFO Queue

![7BF7D8EB-09A6-4418-806D-F66A303C57BF](https://user-images.githubusercontent.com/53600644/194141313-c197f1b7-d24f-41cf-90fe-6d718dcbf512.jpeg)


- high throughput (300/s without batching, 3000/s with batching)
- process messages **in order** by the consumer (**sequencing**)
  - specify **MessageGroupId** to ensure ordering
  - messages with different Group ID may be received out of order
  - messages with the same Group ID are delivered to one consumer at a time
- sent messages exactly **once (deduplication)**
  - provide **MessageDeduplicationId** with message
  - content based duplication: **MessageDeduplicationId** is generated as the SHA-256 of the message body (not attributes)
  - deduplication interval: 5 minutes
- no per message delay (only per **queue delay**)

## SQS Extended Client

- to send messages larger than 256KB ⇒ use the SQS Extended Client

![D6A6F41D-FD62-4F1E-B749-96519C6AB669](https://user-images.githubusercontent.com/53600644/194141368-6f9783a4-1ce2-4949-8060-2870c2dae261.jpeg)


## Security

- encrypt on fly using HTTPS endpoint
- enable SSE using KMS
  - can set CMK (Customer Key)
  - can set encrypt key reuse period (1 minute - 24 hours)
  - only encrypt the body
- SQS queue access policy
  - control over IP
  - control over the time the request come in

API

- CreateQueue, DeleteQueue
- PurgeQueue: delete all messages in queue
- SendMessage, ReceiveMessage, DeleteMessage
- ChangeMessageVisibility: change visibility timeout
- WaitTimeSeconds
- DelaySeconds
- Batch [SendMessage/DeleteMessage/ChangeMessageVisibility]
