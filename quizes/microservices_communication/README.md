# Questions

## 1. What statement is true?

1) Synchronous communication is more reliable because you receive response almost immediately 
2) Asynchronous communication decouples services and makes architecture more resilient
3) Message brokers (Rabbit MQ, ActiveMQ) add complexity to system that’s why should be avoided in any case 
4) Message brokers help to cache GET requests and provide stored response immediately

## 2. Is it possible to have multiple subscriptions on a single message RabbitMQ queue?

1) Yes
2) No

## 3. What is the key characteristic of Fallback?

1) It’s a module that acts as a load balancer, which means it will call different services as per requirements 
2) It defines structured behavior upon a failure 
3) Stores responses and return the same response for GET request 
4) Constrains actions to fixed-size resource pool to prevent failing calls from swamping a resource.

## 4. What statements are true about orchestration? [Choose TWO]

1) Orchestration employs a centralized approach for service composition. 
2) Orchestration employs a decentralized approach for service composition. 
3) The Web services involved in orchestration do not "know" (and do not need to know) that they are involved in a composition process and that they are taking part in a higher-level business process. 
4) All participants in the orchestration need to be aware of the business process, operations to execute, messages to exchange, and the timing of message exchanges.

## 5. What options are essential for lowering DB load in service with 3-tier architecture? [Choose TWO]

1) Store data in CSV on HDD drive instead of DB 
2) Use cached responses for GET requests 
3) Implement CORS for cross-origin requests 
4) Protect system from DDoS attacks

[KEYS](https://epam.sharepoint.com/:x:/r/sites/MicroservicesProgram/Shared%20Documents/Keys.xlsx?d=wd46da4aa1b974aac94b63ef2eef6aad1&csf=1&web=1&e=MNz1cv)