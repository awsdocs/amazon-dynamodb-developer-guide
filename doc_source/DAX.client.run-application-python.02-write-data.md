# 02\-write\-data\.py<a name="DAX.client.run-application-python.02-write-data"></a>

The `02-write-data.py` program writes test data to `TryDaxTable`\.

```
import boto3


def write_data_to_dax_table(key_count, item_size, dyn_resource=None):
    """
    Writes test data to the demonstration table.

    :param key_count: The number of partition and sort keys to use to populate the
                      table. The total number of items is key_count * key_count.
    :param item_size: The size of non-key data for each test item.
    :param dyn_resource: Either a Boto3 or DAX resource.
    """
    if dyn_resource is None:
        dyn_resource = boto3.resource('dynamodb')

    table = dyn_resource.Table('TryDaxTable')
    some_data = 'X' * item_size

    for partition_key in range(1, key_count + 1):
        for sort_key in range(1, key_count + 1):
            table.put_item(Item={
                'partition_key': partition_key,
                'sort_key': sort_key,
                'some_data': some_data
            })
            print(f"Put item ({partition_key}, {sort_key}) succeeded.")


if __name__ == '__main__':
    write_key_count = 10
    write_item_size = 1000
    print(f"Writing {write_key_count*write_key_count} items to the table. "
          f"Each item is {write_item_size} characters.")
    write_data_to_dax_table(write_key_count, write_item_size)
```