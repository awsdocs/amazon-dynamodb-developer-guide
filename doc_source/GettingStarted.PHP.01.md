# Step 1: Create a Table<a name="GettingStarted.PHP.01"></a>

In this step, you create a table named `Movies`\. The primary key for the table is composed of the following two attributes:
+ `year` – The partition key\. The `AttributeType` is `N` for number\.
+ `title` – The sort key\. The `AttributeType` is `S` for string\.

1. Copy and paste the following program into a file named `MoviesCreateTable.php`.

   ```
   <?php
   require 'vendor/autoload.php';
   
   date_default_timezone_set('UTC');
   
   use Aws\DynamoDb\Exception\DynamoDbException;
   
   $sdk = new Aws\Sdk([
       'endpoint'   => 'http://localhost:8000',
       'region'   => 'us-west-2',
       'version'  => 'latest'
   ]);
   
   $dynamodb = $sdk->createDynamoDb();
   
   $params = [
       'TableName' => 'Movies',
       'KeySchema' => [
           [
               'AttributeName' => 'year',
               'KeyType' => 'HASH'  //Partition key
           ],
           [
               'AttributeName' => 'title',
               'KeyType' => 'RANGE'  //Sort key
           ]
       ],
       'AttributeDefinitions' => [
           [
               'AttributeName' => 'year',
               'AttributeType' => 'N'
           ],
           [
               'AttributeName' => 'title',
               'AttributeType' => 'S'
           ],
   
       ],
       'ProvisionedThroughput' => [
           'ReadCapacityUnits' => 10,
           'WriteCapacityUnits' => 10
       ]
   ];
   
   try {
       $result = $dynamodb->createTable($params);
       echo 'Created table.  Status: ' . 
           $result['TableDescription']['TableStatus'] ."\n";
   
   } catch (DynamoDbException $e) {
       echo "Unable to create table:\n";
       echo $e->getMessage() . "\n";
   }
   
   ?>
   ```
**Note**  
You set the endpoint to indicate that you are creating the table in Amazon DynamoDB on your computer\.
In the `createTable` call, you specify the table name, primary key attributes, and its data types\.
The `ProvisionedThroughput` parameter is required, but the downloadable version of DynamoDB ignores it\. \(Provisioned throughput is beyond the scope of this exercise\.\)

1. To run the program, type the following command.

   `php MoviesCreateTable.php`

To learn more about managing tables, see [Working with Tables in DynamoDB](WorkingWithTables.md)\.
