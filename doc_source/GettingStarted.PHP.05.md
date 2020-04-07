# Step 5: \(Optional\) Delete the Table<a name="GettingStarted.PHP.05"></a>

 To delete the `Movies` table: 

1. Copy the following program and paste it into a file named `MoviesDeleteTable.php`\.

   ```
   /**
    * Copyright 2010-2019 Amazon.com, Inc. or its affiliates. All Rights Reserved.
    *
    * This file is licensed under the Apache License, Version 2.0 (the "License").
    * You may not use this file except in compliance with the License. A copy of
    * the License is located at
    *
    * http://aws.amazon.com/apache2.0/
    *
    * This file is distributed on an "AS IS" BASIS, WITHOUT WARRANTIES OR
    * CONDITIONS OF ANY KIND, either express or implied. See the License for the
    * specific language governing permissions and limitations under the License.
   */
   
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
   ```

1. To run the program, enter the following command\.

   `php MoviesDeleteTable.php`