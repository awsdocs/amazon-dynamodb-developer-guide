# 01\-create\-table\.py<a name="DAX.client.run-application-python.01-create-table"></a>

The `01-create-table.py` program creates a table \(`TryDaxTable`\)\. The remaining Python programs in this section depend on this table\.

```
import boto3


def create_dax_table(dyn_resource=None):
    """
    Creates a DynamoDB table.

    :param dyn_resource: Either a Boto3 or DAX resource.
    :return: The newly created table.
    """
    if dyn_resource is None:
        dyn_resource = boto3.resource('dynamodb')

    table_name = 'TryDaxTable'
    params = {
        'TableName': table_name,
        'KeySchema': [
            {'AttributeName': 'partition_key', 'KeyType': 'HASH'},
            {'AttributeName': 'sort_key', 'KeyType': 'RANGE'}
        ],
        'AttributeDefinitions': [
            {'AttributeName': 'partition_key', 'AttributeType': 'N'},
            {'AttributeName': 'sort_key', 'AttributeType': 'N'}
        ],
        'ProvisionedThroughput': {
            'ReadCapacityUnits': 10,
            'WriteCapacityUnits': 10
        }
    }
    table = dyn_resource.create_table(**params)
    print(f"Creating {table_name}...")
    table.wait_until_exists()
    return table


if __name__ == '__main__':
    dax_table = create_dax_table()
    print(f"Created table.")
```