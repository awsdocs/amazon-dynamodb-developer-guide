# DynamoDB Streams and Time to Live<a name="time-to-live-ttl-streams"></a>

You can back up, or otherwise process, items that are deleted by [Time to Live](TTL.md) \(TTL\) by enabling Amazon DynamoDB Streams on the table and processing the streams records of the expired items\.

The streams record contains a user identity field `Records[<index>].userIdentity`\.

Items that are deleted by the Time to Live process after expiration have the following fields:
+ `Records[<index>].userIdentity.type`

  `"Service"`
+ `Records[<index>].userIdentity.principalId`

  `"dynamodb.amazonaws.com"`

The following JSON shows the relevant portion of a single streams record\.

```
"Records":[
    {
        ...

        "userIdentity":{
            "type":"Service",
            "principalId":"dynamodb.amazonaws.com"
        }

        ...

    }
]
```