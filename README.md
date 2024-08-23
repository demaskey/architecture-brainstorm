# Assessment API with Gen AI Utility

```mermaid
flowchart TD
    subgraph Clients
        webClient[Web Client]
        mobileClient[Mobile Client]
    end
    subgraph API
        restApi[REST API]
        db[(Relational DB)]
    end
    subgraph "Asynchronous Request-Reply"
        ai[LLM / Gen AI]
        sqs[SQS]
        sns[SNS]
        lambda[Lambda]
        s3[S3 Bucket]
    end
    idp[Identity Provider]

    webClient --> restApi
    mobileClient --> restApi
    webClient -.- |polling|restApi
    mobileClient  -.- |polling|restApi
    
    restApi --> db
    restApi --> sqs
    restApi --> s3
    restApi -.- |polling|s3
    
    lambda --> sqs
    lambda --> ai
    ai --> s3
    
    sns --- sqs
    sns --- lambda

    idp -.- restApi
```
- Utilize the Asynchronous Request-Reply pattern

# Assessment API
- Scalability
- Reliability
- Don't overwhelm a vender
``` mermaid
flowchart TB
    subgraph Clients
        webClient["Web 
        Client"] 
        mobileClient["Mobile
        Client"]
    end
    subgraph Create Assessment API
        apiCreate["Create Assess. 
        REST API"]
        createdDb[("Relational
        DB")]
    end
    subgraph Take Assessment API
        apiTake["Take Assess.
        REST API"]
        takeDb[("Document
        DB")]
    end
    subgraph Pub/Sub
        sqs[SQS]
        lambda[Lambda]
        sns[SNS]
    end
    subgraph Cache Aside
        redisCache[Redis]
    end
    idp["Identity
    Provider"]

    webClient & mobileClient --> apiCreate & apiTake
    
    apiCreate --> createdDb

    apiCreate --> |pub event|sqs
    sns --- sqs & lambda
    lambda --> sqs & apiTake

    apiTake --> takeDb

    apiTake --> redisCache
    apiTake -.- |Retry & Circuit Breaker|idp
    redisCache -.- idp
```
