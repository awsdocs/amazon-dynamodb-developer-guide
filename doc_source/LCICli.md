# Working with Local Secondary Indexes: AWS CLI<a name="LCICli"></a>

You can use the AWS CLI to create an Amazon DynamoDB table with one or more local secondary indexes, describe the indexes on the table, and perform queries using the indexes\.

**Topics**
+ [Create a Table with a Local Secondary Index](#LCICli.CreateTableWithIndex)
+ [Describe a Table with a Local Secondary Index](#LCICli.DescribeTableWithIndex)
+ [Query a Local Secondary Index](#LCICli.QueryAnIndex)

## Create a Table with a Local Secondary Index<a name="LCICli.CreateTableWithIndex"></a>

Local secondary indexes must be created at the same time you create a table\. To do this, use the `create-table` parameter and provide your specifications for one or more local secondary indexes\. The following example creates a table \(`Music`\) to hold information about songs in a music collection\. The partition key is `Artist` and the sort key is `SongTitle`\. A secondary index, `AlbumTitleIndex` on the `AlbumTitle` attribute facilitates queries by album title\. 

```
aws dynamodb create-table \
    --table-name Music \
    --attribute-definitions AttributeName=Artist,AttributeType=S AttributeName=SongTitle,AttributeType=S \
        AttributeName=AlbumTitle,AttributeType=S  \
    --key-schema AttributeName=Artist,KeyType=HASH AttributeName=SongTitle,KeyType=RANGE \
    --provisioned-throughput \
        ReadCapacityUnits=10,WriteCapacityUnits=5 \
    --local-secondary-indexes \
        "[{\"IndexName\": \"AlbumTitleIndex\",
        \"KeySchema\":[{\"AttributeName\":\"Artist\",\"KeyType\":\"HASH\"},
                      {\"AttributeName\":\"AlbumTitle\",\"KeyType\":\"RANGE\"}],
        \"Projection\":{\"ProjectionType\":\"INCLUDE\",  \"NonKeyAttributes\":[\"Genre\", \"Year\"]}}]"
```

You must wait until DynamoDB creates the table and sets the table status to `ACTIVE`\. After that, you can begin putting data items into the table\. You can use [describe\-table](https://docs.aws.amazon.com/cli/latest/reference/dynamodb/describe-table.html) to determine the status of the table creation\. 

## Describe a Table with a Local Secondary Index<a name="LCICli.DescribeTableWithIndex"></a>

To get information about local secondary indexes on a table, use the `describe-table` parameter\. For each index, you can access its name, key schema, and projected attributes\.

```
aws dynamodb describe-table --table-name Music
```

## Query a Local Secondary Index<a name="LCICli.QueryAnIndex"></a>

You can use the `query` operation on a local secondary index in much the same way that you `query` a table\. You must specify the index name, the query criteria for the index sort key, and the attributes that you want to return\. In this example, the index is `AlbumTitleIndex` and the index sort key is `AlbumTitle`\. 

The only attributes returned are those that have been projected into the index\. You could modify this query to select non\-key attributes too, but this would require table fetch activity that is relatively expensive\. For more information about table fetches, see [Attribute Projections](LSI.md#LSI.Projections)\.

```
aws dynamodb query \
    --table-name Music \
    --index-name AlbumTitleIndex \
    --key-condition-expression "Artist = :v_artist and AlbumTitle = :v_title" \
    --expression-attribute-values  '{":v_artist":{"S":"Acme Band"},":v_title":{"S":"Songs About Life"} }'
```