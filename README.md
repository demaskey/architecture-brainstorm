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
flowchart 
    webClient["Web 
    Client"] 
    apiCreate["Create Assess. 
    REST API"]
    mobileClient["Mobile
    Client"]
    apiTake["Take Assess.
    REST API"]

    webClient & mobileClient --> apiCreate & apiTake

    createdDb[("Relational
    DB")]
    apiCreate --> createdDb

    apiCreate --> |pub event|sqs[SQS]
    sns[SNS] --- sqs & lambda[Lambda]
    lambda --> sqs & apiTake

    takeDb[("Document
    DB")]
    apiTake --> takeDb

    redisCache[Redis]
    idp["Identity
    Provider"]

    apiTake --> redisCache & idp
```
