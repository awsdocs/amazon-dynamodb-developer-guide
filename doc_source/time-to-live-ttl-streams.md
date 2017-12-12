# DynamoDB Streams and Time To Live<a name="time-to-live-ttl-streams"></a>

You can back up, or otherwise process, items deleted by Time To Live by enabling Amazon DynamoDB Streams on the table and processing the Streams records of the expired items\. 

The Streams record contains a user identity field `Records[<index>].userIdentity`\.

Items that are deleted by the Time To Live process after expiration have the following fields:

+ `Records[<index>].userIdentity.type`

  `"Service"`

+ `Records[<index>].userIdentity.principalId`

  `"dynamodb.amazonaws.com"`

The following JSON shows the relevant portion of a single Streams record\.

```
"Records":[
    {
        ...

        "userIdentity":{
            "type":"Service",
            "principalId":"dynamodb.amazonaws.com"
        }

        ...

    ]}
```

Items deleted by other users will show the `principalId` of the account used to delete the items\.