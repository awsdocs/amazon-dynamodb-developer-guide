# DynamoDB transactions example<a name="transaction-example"></a>

As an example of a situation in which Amazon DynamoDB transactions can be useful, consider this sample Java application for an online marketplace\.

The application has three DynamoDB tables in the backend:
+ `Customers` — This table stores details about the marketplace customers\. Its primary key is a `CustomerId` unique identifier\.
+ `ProductCatalog` — This table stores details such as price and availability about the products for sale in the marketplace\. Its primary key is a `ProductId` unique identifier\.
+ `Orders` — This table stores details about orders from the marketplace\. Its primary key is an `OrderId` unique identifier\.

## Making an order<a name="transaction-example-write-order"></a>

The following code snippets illustrate how to use DynamoDB transactions to coordinate the multiple steps that are required to create and process an order\. Using a single all\-or\-nothing operation ensures that if any part of the transaction fails, no actions in the transaction are run and no changes are made\.

In this example, you set up an order from a customer whose `customerId` is `09e8e9c8-ec48`\. You then run it as a single transaction using the following simple order\-processing workflow:

1. Determine that the customer ID is valid\.

1. Make sure that the product is `IN_STOCK`, and update the product status to `SOLD`\.

1. Make sure that the order does not already exist, and create the order\.

### Validate the customer<a name="transaction-example-order-part-a"></a>

First, define an action to verify that a customer with `customerId` equal to `09e8e9c8-ec48` exists in the customer table\.

```
final String CUSTOMER_TABLE_NAME = "Customers";
final String CUSTOMER_PARTITION_KEY = "CustomerId";
final String customerId = "09e8e9c8-ec48";
final HashMap<String, AttributeValue> customerItemKey = new HashMap<>();
customerItemKey.put(CUSTOMER_PARTITION_KEY, new AttributeValue(customerId));

ConditionCheck checkCustomerValid = new ConditionCheck()
    .withTableName(CUSTOMER_TABLE_NAME)
    .withKey(customerItemKey)
    .withConditionExpression("attribute_exists(" + CUSTOMER_PARTITION_KEY + ")");
```

### Update the product status<a name="transaction-example-order-part-b"></a>

Next, define an action to update the product status to `SOLD` if the condition that the product status is currently set to `IN_STOCK` is `true`\. Setting the `ReturnValuesOnConditionCheckFailure` parameter returns the item if the item's product status attribute was not equal to `IN_STOCK`\.

```
final String PRODUCT_TABLE_NAME = "ProductCatalog";
final String PRODUCT_PARTITION_KEY = "ProductId";
HashMap<String, AttributeValue> productItemKey = new HashMap<>();
productItemKey.put(PRODUCT_PARTITION_KEY, new AttributeValue(productKey));

Map<String, AttributeValue> expressionAttributeValues = new HashMap<>();
expressionAttributeValues.put(":new_status", new AttributeValue("SOLD"));
expressionAttributeValues.put(":expected_status", new AttributeValue("IN_STOCK"));

Update markItemSold = new Update()
    .withTableName(PRODUCT_TABLE_NAME)
    .withKey(productItemKey)
    .withUpdateExpression("SET ProductStatus = :new_status")
    .withExpressionAttributeValues(expressionAttributeValues)
    .withConditionExpression("ProductStatus = :expected_status")
    .withReturnValuesOnConditionCheckFailure(ReturnValuesOnConditionCheckFailure.ALL_OLD);
```

### Create the order<a name="transaction-example-order-part-c"></a>

Lastly, create the order as long as an order with that `OrderId` does not already exist\.

```
final String ORDER_PARTITION_KEY = "OrderId";
final String ORDER_TABLE_NAME = "Orders";

HashMap<String, AttributeValue> orderItem = new HashMap<>();
orderItem.put(ORDER_PARTITION_KEY, new AttributeValue(orderId));
orderItem.put(PRODUCT_PARTITION_KEY, new AttributeValue(productKey));
orderItem.put(CUSTOMER_PARTITION_KEY, new AttributeValue(customerId));
orderItem.put("OrderStatus", new AttributeValue("CONFIRMED"));
orderItem.put("OrderTotal", new AttributeValue("100"));

Put createOrder = new Put()
    .withTableName(ORDER_TABLE_NAME)
    .withItem(orderItem)
    .withReturnValuesOnConditionCheckFailure(ReturnValuesOnConditionCheckFailure.ALL_OLD)
    .withConditionExpression("attribute_not_exists(" + ORDER_PARTITION_KEY + ")");
```

### Run the transaction<a name="transaction-example-order-part-d"></a>

The following example illustrates how to run the actions defined previously as a single all\-or\-nothing operation\.

```
    Collection<TransactWriteItem> actions = Arrays.asList(
        new TransactWriteItem().withConditionCheck(checkCustomerValid),
        new TransactWriteItem().withUpdate(markItemSold),
        new TransactWriteItem().withPut(createOrder));

    TransactWriteItemsRequest placeOrderTransaction = new TransactWriteItemsRequest()
        .withTransactItems(actions)
        .withReturnConsumedCapacity(ReturnConsumedCapacity.TOTAL);

    // Run the transaction and process the result.
    try {
        client.transactWriteItems(placeOrderTransaction);
        System.out.println("Transaction Successful");

    } catch (ResourceNotFoundException rnf) {
        System.err.println("One of the table involved in the transaction is not found" + rnf.getMessage());
    } catch (InternalServerErrorException ise) {
        System.err.println("Internal Server Error" + ise.getMessage());
    } catch (TransactionCanceledException tce) {
        System.out.println("Transaction Canceled " + tce.getMessage());
    }
```

## Reading the order details<a name="transaction-example-read-order"></a>

The following example shows how to read the completed order transactionally across the `Orders` and `ProductCatalog` tables\.

```
HashMap<String, AttributeValue> productItemKey = new HashMap<>();
productItemKey.put(PRODUCT_PARTITION_KEY, new AttributeValue(productKey));

HashMap<String, AttributeValue> orderKey = new HashMap<>();
orderKey.put(ORDER_PARTITION_KEY, new AttributeValue(orderId));

Get readProductSold = new Get()
    .withTableName(PRODUCT_TABLE_NAME)
    .withKey(productItemKey);
Get readCreatedOrder = new Get()
    .withTableName(ORDER_TABLE_NAME)
    .withKey(orderKey);

Collection<TransactGetItem> getActions = Arrays.asList(
    new TransactGetItem().withGet(readProductSold),
    new TransactGetItem().withGet(readCreatedOrder));

TransactGetItemsRequest readCompletedOrder = new TransactGetItemsRequest()
    .withTransactItems(getActions)
    .withReturnConsumedCapacity(ReturnConsumedCapacity.TOTAL);

// Run the transaction and process the result.
try {
    TransactGetItemsResult result = client.transactGetItems(readCompletedOrder);
    System.out.println(result.getResponses());
} catch (ResourceNotFoundException rnf) {
    System.err.println("One of the table involved in the transaction is not found" + rnf.getMessage());
} catch (InternalServerErrorException ise) {
    System.err.println("Internal Server Error" + ise.getMessage());
} catch (TransactionCanceledException tce) {
    System.err.println("Transaction Canceled" + tce.getMessage());
}
```

## Additional examples<a name="transaction-example-Additional"></a>
+ [Using transactions from DynamoDBMapper](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/DynamoDBMapper.Transactions.html) 