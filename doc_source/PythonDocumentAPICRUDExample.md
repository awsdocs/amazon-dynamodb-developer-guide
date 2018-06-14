# Example: CRUD Operations Using the AWS SDK for Python Document API<a name="PythonDocumentAPICRUDExample"></a>

The following code sample illustrates CRUD operations on an item\. The example creates an item, retrieves it, performs various updates, and finally deletes the item\.


**Note**  
This code sample assumes that you have already loaded data into DynamoDB for your account by following the instructions in the [Creating Tables and Loading Sample Data](SampleData.md) section\.  

```
from __future__ import print_function # Python 2/3 compatibility
import boto3
import json
import decimal

TABLE_NAME = 'ProductCatalog'

# Helper class to convert a DynamoDB item to JSON.
class DecimalEncoder(json.JSONEncoder):
    def default(self, o):
        if isinstance(o, decimal.Decimal):
            if o % 1 > 0:
                return float(o)
            else:
                return int(o)
        return super(DecimalEncoder, self).default(o)

def create_items():
    try:
      response = table.put_item(
         Item={
              "Id" : 120,
              "Title": "Book 120 Title",
              "ISBN": "120-1111111111",
              "Authors": ["Author12", "Author22"],
              "Price": 20,
              "Dimensions": "8.5x11.0x.75",
              "PageCount": 500,
              "InPublication": False,
              "ProductCategory": "Book"
          }
      )
      print("put_item1 succeeded:")
      print(json.dumps(response, indent=4))

      response = table.put_item(
         Item={
              "Id" : 121,
              "Title": "Book 121 Title",
              "ISBN": "121-1111111111",
              "Authors": ["Author12", "Author22"],
              "Price": 20,
              "Dimensions": "8.5x11.0x.75",
              "PageCount": 500,
              "InPublication": True,
              "ProductCategory": "Book"
          }
      )
      print("put_item2 succeeded:")
      print("create_items succeeded:")
      print(json.dumps(response, indent=4, cls=DecimalEncoder))
    except ClientError as e:
      print(e.response['Error']['Message'])

def retrieve_item():
    try:
        response = table.get_item(
            Key={
                "Id": 120
            },
            AttributesToGet=[
                "Id",
                "ISBN",
                "Title",
                "Authors"
            ]
        )
    except ClientError as e:
        print(e.response['Error']['Message'])
    else:
        item = response['Item']
        print("retrieve_item succeeded:")
        print(json.dumps(item, indent=4, cls=DecimalEncoder))

def update_add_new_attribute():
    try:
        response = table.update_item(
            Key={
                "Id": 120
            },
            UpdateExpression="set #na = :val1",
            ExpressionAttributeNames={
                "#na": "NewAttribute"
            },
            ExpressionAttributeValues={
                ":val1": 1
            },
            ReturnValues="UPDATED_NEW"
        )
    except ClientError as e:
        print(e.response['Error']['Message'])
    else:
        print("update_add_new_attribute succeeded:")
        print(json.dumps(response, indent=4, cls=DecimalEncoder))

def update_multiple_attributes():
    try:
        response = table.update_item(
            Key={
                "Id": 120
            },
            UpdateExpression="SET #a = :val1 ADD #na :val2",
            ExpressionAttributeNames={
                "#a": "Authors",
                "#na": "NewAttribute"
            },
            ExpressionAttributeValues={
                ":val1": {
                    "SS" : ["Author YY","Author ZZ"]
                },
                ":val2": 5
            },
            ReturnValues="UPDATED_NEW"
        )
    except ClientError as e:
        print(e.response['Error']['Message'])
    else:
        print("update_multiple_attributes succeeded:")
        print(json.dumps(response, indent=4, cls=DecimalEncoder))

def update_existing_attribute_conditionally():
    try:
        response = table.update_item(
            Key={
                "Id": 120
            },
            UpdateExpression="set #p = :val1",
            ExpressionAttributeNames={
                "#p": "Price"
            },
            ExpressionAttributeValues={
                ":val1": 25,
                ":val2": 20
            },
            ConditionExpression="#p = :val2",
            ReturnValues="UPDATED_NEW"
        )
    except ClientError as e:
        print(e.response['Error']['Message'])
    else:
        print("update_existing_attribute_conditionally succeeded:")
        print(json.dumps(response, indent=4, cls=DecimalEncoder))

def delete_item():
    try:
        response = table.delete_item(
            Key={
                "Id": 120
            },
            ExpressionAttributeNames={
                "#ip": "InPublication"
            },
            ExpressionAttributeValues={
                ":val": False
            },
            ConditionExpression="#ip = :val",
            ReturnValues="ALL_OLD"
        )
    except ClientError as e:
        print(e.response['Error']['Message'])
    else:
        print("delete_item succeeded:")
        print(json.dumps(response, indent=4, cls=DecimalEncoder))

if __name__ == "__main__":

    dynamodb = boto3.resource('dynamodb')
    table = dynamodb.Table(TABLE_NAME)

    # Create Items.
    create_items()

    # Get an Item.
    retrieve_item()

    #Perform different kinds of updates.
    update_add_new_attribute()
    update_multiple_attributes()
    update_existing_attribute_conditionally()

    #Delete an Item.
    delete_item()

```
