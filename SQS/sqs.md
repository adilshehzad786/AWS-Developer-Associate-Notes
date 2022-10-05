# SQS

![SQS%20df1fe8d25db9488985806af5c547307b/E3A330F49B42409B8248839DC5113426jpeg](file://C:\Users\ashehzad\Downloads\Export-530445e9-18cc-4959-b60a-18ba8a69f851\SQS%20df1fe8d25db9488985806af5c547307b\E3A330F4-9B42-409B-8248-839DC5113426.jpeg)

## Application communication

- synchronous communications
  
  ![SQS%20df1fe8d25db9488985806af5c547307b/0B8C1B7A0867446EA46E8489681F86CEjpeg](file://C:\Users\ashehzad\Downloads\Export-530445e9-18cc-4959-b60a-18ba8a69f851\SQS%20df1fe8d25db9488985806af5c547307b\0B8C1B7A-0867-446E-A46E-8489681F86CE.jpeg)
  
  ⇒ can be problematic if there are sudden spikes of traffic
  
- asynchronous / event based
  
  ![SQS%20df1fe8d25db9488985806af5c547307b/9E25FC2991BC49D09746B534E73F0BFFjpeg](file://C:\Users\ashehzad\Downloads\Export-530445e9-18cc-4959-b60a-18ba8a69f851\SQS%20df1fe8d25db9488985806af5c547307b\9E25FC29-91BC-49D0-9746-B534E73F0BFF.jpeg)
  
  ⇒ scalable
  

## Producing messages

- define body
- add message attributes (metadata - optional)
- delay delivery (optional)
- get back
  - message identifier
  - MD5 hash of body

![SQS%20df1fe8d25db9488985806af5c547307b/C4AE4E3DC9CE4CBBA7E829D591156D7Bpng](file://C:\Users\ashehzad\Downloads\Export-530445e9-18cc-4959-b60a-18ba8a69f851\SQS%20df1fe8d25db9488985806af5c547307b\C4AE4E3D-C9CE-4CBB-A7E8-29D591156D7B.png)

## Consuming messages

- poll message from SQS (maximum 10 messages at a time)
- process the message within the visibility timeout
- delete the message using message ID & receipt handle

![SQS%20df1fe8d25db9488985806af5c547307b/3B962D1172104CD89C6076DF91E522F8jpeg](file://C:\Users\ashehzad\Downloads\Export-530445e9-18cc-4959-b60a-18ba8a69f851\SQS%20df1fe8d25db9488985806af5c547307b\3B962D11-7210-4CD8-9C60-76DF91E522F8.jpeg)

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
  

![SQS%20df1fe8d25db9488985806af5c547307b/38EDDEFDFFC34DA596DA11134B733336jpeg](file://C:\Users\ashehzad\Downloads\Export-530445e9-18cc-4959-b60a-18ba8a69f851\SQS%20df1fe8d25db9488985806af5c547307b\38EDDEFD-FFC3-4DA5-96DA-11134B733336.jpeg)

- long polling

![SQS%20df1fe8d25db9488985806af5c547307b/B46EC6B8E5194F05BC0A55B9BF4C8245png](file://C:\Users\ashehzad\Downloads\Export-530445e9-18cc-4959-b60a-18ba8a69f851\SQS%20df1fe8d25db9488985806af5c547307b\B46EC6B8-E519-4F05-BC0A-55B9BF4C8245.png)

## Standard Queue

![SQS%20df1fe8d25db9488985806af5c547307b/E6E72E5AC02B45F483EDE60EE47F331Ejpeg](file://C:\Users\ashehzad\Downloads\Export-530445e9-18cc-4959-b60a-18ba8a69f851\SQS%20df1fe8d25db9488985806af5c547307b\E6E72E5A-C02B-45F4-83ED-E60EE47F331E.jpeg)

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

![SQS%20df1fe8d25db9488985806af5c547307b/E6485E57AEE1487CBE0D89F1AAC5B0D1jpeg](file://C:\Users\ashehzad\Downloads\Export-530445e9-18cc-4959-b60a-18ba8a69f851\SQS%20df1fe8d25db9488985806af5c547307b\E6485E57-AEE1-487C-BE0D-89F1AAC5B0D1.jpeg)

## FIFO Queue

![SQS%20df1fe8d25db9488985806af5c547307b/7BF7D8EB09A64418806DF66A303C57BFjpeg](file://C:\Users\ashehzad\Downloads\Export-530445e9-18cc-4959-b60a-18ba8a69f851\SQS%20df1fe8d25db9488985806af5c547307b\7BF7D8EB-09A6-4418-806D-F66A303C57BF.jpeg)

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

![SQS%20df1fe8d25db9488985806af5c547307b/D6A6F41DFD624F1EB74996519C6AB669jpeg](file://C:\Users\ashehzad\Downloads\Export-530445e9-18cc-4959-b60a-18ba8a69f851\SQS%20df1fe8d25db9488985806af5c547307b\D6A6F41D-FD62-4F1E-B749-96519C6AB669.jpeg)

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