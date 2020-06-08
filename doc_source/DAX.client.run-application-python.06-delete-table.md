# 06\-delete\-table\.py<a name="DAX.client.run-application-python.06-delete-table"></a>

The `06-delete-table.py` program deletes `TryDaxTable`\. Run this program after you have finished testing Amazon DynamoDB Accelerator \(DAX\) functionality\.

```
import boto3


def delete_dax_table(dyn_resource=None):
    """
    Deletes the demonstration table.

    :param dyn_resource: Either a Boto3 or DAX resource.
    """
    if dyn_resource is None:
        dyn_resource = boto3.resource('dynamodb')

    table = dyn_resource.Table('TryDaxTable')
    table.delete()

    print(f"Deleting {table.name}...")
    table.wait_until_not_exists()


if __name__ == '__main__':
    delete_dax_table()
    print("Table deleted!")
```