# IAM Security Policies with PartiQL for DynamoDB<a name="ql-iam"></a>

## <a name="ql-iam.permissions-model"></a>

The following permissions are required:
+ To read items using PartiQL for DynamoDB, you must have `dynamodb:PartiQLSelect` permission on the table or index\.
+ To insert items using PartiQL for DynamoDB, you must have `dynamodb:PartiQLInsert` permission on the table or index\.
+ To update items using PartiQL for DynamoDB, you must have `dynamodb:PartiQLUpdate` permission on the table or index\.
+ To delete items using PartiQL for DynamoDB, you must have `dynamodb:PartiQLDelete` permission on the table or index\.

## Example: Allow all PartiQL for DynamoDB statements \(Select/Insert/Update/Delete\) on a table<a name="access-policy-ql-iam-example1"></a>

The following IAM policy grants permissions to run all PartiQL for DynamoDB statements on a table\. 

```
{
   "Version":"2012-10-17",
   "Statement":[
      {
         "Effect":"Allow",
         "Action":[
            "dynamodb:PartiQLInsert",
            "dynamodb:PartiQLUpdate",
            "dynamodb:PartiQLDelete",
            "dynamodb:PartiQLSelect"
         ],
         "Resource":[
            "arn:aws:dynamodb:us-west-2:123456789012:table/Music"
         ]
      }
   ]
}
```

## Example: Allow PartiQL for DynamoDB select statements on a table<a name="access-policy-ql-iam-example2"></a>

The following IAM policy grants permissions to run the `select` statement on a specific table\.

```
{
   "Version":"2012-10-17",
   "Statement":[
      {
         "Effect":"Allow",
         "Action":[
            "dynamodb:PartiQLSelect"
         ],
         "Resource":[
            "arn:aws:dynamodb:us-west-2:123456789012:table/Music"
         ]
      }
   ]
}
```

## Example: Allow PartiQL for DynamoDB insert statements on an index<a name="access-policy-ql-iam-example3"></a>

The following IAM policy grants permissions to run the `insert` statement on a specific index\. 

```
{
   "Version":"2012-10-17",
   "Statement":[
      {
         "Effect":"Allow",
         "Action":[
            "dynamodb:PartiQLInsert"
         ],
         "Resource":[
            "arn:aws:dynamodb:us-west-2:123456789012:table/Music/index/index1"
         ]
      }
   ]
}
```

## Example: Allow PartiQL for DynamoDB transactional statements only on a table<a name="access-policy-ql-iam-example4"></a>

The following IAM policy grants permissions to run only transactional statements on a specific table\. 

```
{
   "Version":"2012-10-17",
   "Statement":[
      {
         "Effect":"Allow",
         "Action":[
            "dynamodb:PartiQLInsert",
            "dynamodb:PartiQLUpdate",
            "dynamodb:PartiQLDelete",
            "dynamodb:PartiQLSelect"
         ],
         "Resource":[
            "arn:aws:dynamodb:us-west-2:123456789012:table/Music"
         ],
         "Condition":{
            "StringEquals":{
               "dynamodb:EnclosingOperation":[
                  "ExecuteTransaction"
               ]
            }
         }
      }
   ]
}
```

## Example: Allow PartiQL for DynamoDB non\-transactional reads and writes and block PartiQL transactional reads and writes transactional statements on a table\.<a name="access-policy-ql-iam-example5"></a>

 The following IAM policy grants permissions to run PartiQL for DynamoDB non\-transactional reads and writes while blocking PartiQL for DynamoDB transactional reads and writes\.

```
{
   "Version":"2012-10-17",
   "Statement":[
      {
         "Effect":"Deny",
         "Action":[
            "dynamodb:PartiQLInsert",
            "dynamodb:PartiQLUpdate",
            "dynamodb:PartiQLDelete",
            "dynamodb:PartiQLSelect"
         ],
         "Resource":[
            "arn:aws:dynamodb:us-west-2:123456789012:table/Music"
         ],
         "Condition":{
            "StringEquals":{
               "dynamodb:EnclosingOperation":[
                  "ExecuteTransaction"
               ]
            }
         }
      },
      {
         "Effect":"Allow",
         "Action":[
            "dynamodb:PartiQLInsert",
            "dynamodb:PartiQLUpdate",
            "dynamodb:PartiQLDelete",
            "dynamodb:PartiQLSelect"
         ],
         "Resource":[
            "arn:aws:dynamodb:us-west-2:123456789012:table/Music"
         ]
      }
   ]
}
```

## Example: Allow Select statements and deny full table scan statements in PartiQL for DynamoDB<a name="access-policy-ql-iam-example6"></a>

The following IAM policy grants permissions to run the `select` statement on a specific table while blocking `select` statements that result in a full table scan\.

```
{
   "Version":"2012-10-17",
   "Statement":[
      {
         "Effect":"Deny",
         "Action":[
            "dynamodb:PartiQLSelect"
         ],
         "Resource":[
            "arn:aws:dynamodb:us-west-2:123456789012:table/WatchList"
         ],
         "Condition":{
            "Bool":{
               "dynamodb:FullTableScan":[
                  "true"
               ]
            }
         }
      },
      {
         "Effect":"Allow",
         "Action":[
            "dynamodb:PartiQLSelect"
         ],
         "Resource":[
            "arn:aws:dynamodb:us-west-2:123456789012:table/WatchList"
         ]
      }
   ]
}
```