# 01\-create\-table\.py<a name="DAX.client.run-application-python.01-create-table"></a>

The `01-create-table.py` program creates a table \(*TryDaxTable*\)\. The remaining Python programs in this section depend on this table\.

```
#!/usr/bin/env python3
from __future__ import print_function

import os
import amazondax
import botocore.session

region = os.environ.get('AWS_DEFAULT_REGION', 'us-west-2')

session = botocore.session.get_session()
dynamodb = session.create_client('dynamodb', region_name=region) # low-level client

table_name = "TryDaxTable"

params = {
    'TableName' : table_name,
    'KeySchema': [       
        { 'AttributeName': "pk", 'KeyType': "HASH"},    # Partition key
        { 'AttributeName': "sk", 'KeyType': "RANGE" }   # Sort key
    ],
    'AttributeDefinitions': [       
        { 'AttributeName': "pk", 'AttributeType': "N" },
        { 'AttributeName': "sk", 'AttributeType': "N" }
    ],
    'ProvisionedThroughput': {       
        'ReadCapacityUnits': 10, 
        'WriteCapacityUnits': 10
    }
}

# Create the table
dynamodb.create_table(**params)

# Wait for the table to exist before exiting
print('Waiting for', table_name, '...')
waiter = dynamodb.get_waiter('table_exists')
waiter.wait(TableName=table_name)
```