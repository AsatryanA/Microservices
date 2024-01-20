# Table of Content

 - [What to do](#what-to-do)
 - [Sub-task 1: New Storage API](#sub-task-1-new-storage-api)
 - [Sub-task 2: Resilience](#sub-task-2-resilience)

## What to do

There are few options of implementation of circuit breaker in microservices communications.

The current implementation from the previous tasks do not have any near-static data, so in order to emulate a stubbed circuit breaker, we need to add one more element that emulates this data.
1. Now our files can be in different states depends on processing phase: STAGING - file in processing, PERMANENT - file has been successfully processed;
2. To store and manage this states we will create Storage service. It will be asked by resource service for state details;
3. For case when Storage service in unavailable, we will extend resource service to use advantages of fault tolerance pattern.


## Sub-task 1: New Storage API

1) Create and implement new independent microservice with CRUD API which represents Storage concept.
   The service will be used for management of **storage** types.  
   Prior to this task, the **Resource Service** used its own configuration to access data stores (**s3 buckets**). Now this places configuration goes to new service.  
   When **Storage Service** starts at least two different storage types (**Storage object**) should be created in database by default which will be used by **Resource service**.
   The same storage buckets should be created in **localstack**.  
   **Storage object** can have only the following **storageType** values: **STAGING** or **PERMANENT**. One for **permanent** storage, another for **staging**. They should have different paths of buckets.
   Other storages can also be added via the Storage Service API.

<table>
    <tr>
        <td><b>POST /storages</b></td>
        <td colspan="6"><i>Create new storage</i></td>
    </tr>
    <tr>
        <td rowspan="2"><b>Request</b></td>
        <td>Parameter</td>
        <td>Description</td>
        <td>Restriction</td>
        <td>Body example</td>
        <td>Description</td>
        <td>Restriction</td>
    </tr>
    <tr>
        <td></td>
        <td></td>
        <td></td>
        <td><p>{ "storageType": "PERMANENT",</p>
            <p>&nbsp;&nbsp;&nbsp;&nbsp;"bucket": "bucket_name",</p>
            <p>&nbsp;&nbsp;&nbsp;&nbsp;"path": "/files"}</p></td>
        <td>Content type – application/json Data representing particular storage place\space for binary data.</td>
        <td></td>
    </tr>
    <tr>
        <td rowspan="2"><b>Response</b></td>
        <td colspan="2">Body</td>
        <td colspan="2">Description</td>
        <td colspan="2"><i>Code</i></td>
    </tr>
    <tr>
        <td colspan="2"><p>{</p>
            <p>&nbsp;&nbsp;&nbsp;&nbsp;"id":1123</p>
            <p>}</p>
        </td>
        <td colspan="2">Integer id – ID of created storage</td>
        <td colspan="2"><p>200 – OK</p>
                        <p>400 – Validation error or request body is an invalid MP3</p>
                        <p>500 – Internal server error occurred.</p>
        </td>
    </tr>
    <tr>
        <td><b>GET /storages</b></td>
        <td colspan="6"><i>Gets storages list</i></td>
    </tr>
    <tr>
        <td rowspan="2"><b>Request</b></td>
        <td>Parameter</td>
        <td>Description</td>
        <td>Restriction</td>
        <td>Body example</td>
        <td>Description</td>
        <td>Restriction</td>
    </tr>
    <tr>
        <td></td>
        <td></td>
        <td></td>
        <td></td>
        <td></td>
        <td></td>
    </tr>
    <tr>
        <td rowspan="2"><b>Response</b></td>
        <td colspan="3">Body</td>
        <td>Description</td>
        <td colspan="2">Code</td>
    </tr>
    <tr>
        <td colspan="3">
            <p>&nbsp;&nbsp;&nbsp;&nbsp;"id": "1",</p>
            <p>&nbsp;&nbsp;&nbsp;&nbsp;"storageType": "PERMANENT",</p>
            <p>&nbsp;&nbsp;&nbsp;&nbsp;"bucket": "bucket_name",</p>
            <p>&nbsp;&nbsp;&nbsp;&nbsp;"path": "/files"}]</p></td>
        <td>Existing storages list </td>
        <td colspan="2"><p>200 – OK</p>
                        <p>500 – Internal server error occurred.</p>
        </td>
    </tr>
    <tr>
        <td><b>DELETE /storages?id=1,2</b></td>
        <td colspan="6"><i>Delete a resource oDelete storages by given ids (if storage for id is not presented – do nothing) r resources (song or songs). If song for id is not present – do nothing</i></td>
    </tr>
    <tr>
        <td rowspan="2"><b>Request</b></td>
        <td>Parameter</td>
        <td>Description</td>
        <td>Restriction</td>
        <td>Body example</td>
        <td>Description</td>
        <td>Restriction</td>
    </tr>
    <tr>
        <td>String id</td>
        <td>CSV of storage ids to delete</td>
        <td>Valid CSV Length < 200 characters</td>
        <td></td>
        <td></td>
        <td></td>
    </tr>
    <tr>
        <td rowspan="2"><b>Response</b></td>
        <td colspan="3">Body</td>
        <td>Description</td>
        <td colspan="2">Code</td>
    </tr>
    <tr>
        <td colspan="3"><p>{</p>
            <p>&nbsp;&nbsp;&nbsp;&nbsp;"ids": [1,2]</p>
            <p>}</p>
        </td>
        <td>Integer [] ids – ids of deleted storages</td>
        <td colspan="2"><p>200 – OK</p>
                        <p>500 – Internal server error occurred.</p>
        </td>
    </tr>
</table>

   ! Please pay attention that in local db we store common information about storage types, not about each file state.

2) Update system to interact with new storage service:
   Depends on file processing state we will store it in different location. (Different path/folder for file). To find out details about each state and appropriate storage path we will call storage service.

   So when new file comes to Resource service for processing, we save file to STAGING storage, update file state and path in local DB, then send the file foe further processing.
   When resource service receives signal from resource processor that file has been successfully processed, we change state of file to PERMANENT and update link in resource service local db.
   
   The diagrams below will help you understand what we are talking about.
 
   ![](images/taskpng.png)


   ![](images/fault_tollerance.png)

Note: You can add more file states depends on your systems needs, but let's start with two to decrease system complexity.



## Sub-task 2: Resilience

And finally we implement circuit breaker pattern.
Imagine that storage service is down, but we do not want to make our users wait until we repair services.
So for such cases, when storage service in unavailable, we will store stub data in resource service to emulate response from storage service.
Emulates response should be the same as storage service would return.

1) Add a [Resilience4j](https://mvnrepository.com/artifact/io.github.resilience4j/resilience4j-circuitbreaker) library for **Resource Service**. 
2) Add the circuit breaker config to services (when calling **Storage API** get storages for storing processed files).
3) When an exception returns from the called service provide returning of the stub result.
4) Simulate a failure of the service by shutting down a called service (**Storage API Service**) and test the circuit breaker.
