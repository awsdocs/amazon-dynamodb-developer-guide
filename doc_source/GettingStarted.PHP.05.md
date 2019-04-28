# Step 5: \(Optional\) Delete the Table<a name="GettingStarted.PHP.05"></a>

 Do the following to delete the `Movies` table.

1. Copy and paste the following program into a file named `MoviesDeleteTable.php`.

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
       'TableName' => 'Movies'
   ];
   
   try {
       $result = $dynamodb->deleteTable($params);
       echo "Deleted table.\n";
   
   } catch (DynamoDbException $e) {
       echo "Unable to delete table:\n";
       echo $e->getMessage() . "\n";
   }
   
   ?>
   ```

1. To run the program, type the following command.

   `php MoviesDeleteTable.php`
