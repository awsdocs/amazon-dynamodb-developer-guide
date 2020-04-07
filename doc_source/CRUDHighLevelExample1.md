# Example: CRUD Operations Using the AWS SDK for \.NET Object Persistence Model<a name="CRUDHighLevelExample1"></a>

The following C\# code example declares a `Book` class with `Id`, `Title`, `ISBN`, and `Authors` properties\. The example uses object persistence attributes to map these properties to the `ProductCatalog` table in Amazon DynamoDB\. The example then uses the `DynamoDBContext` to illustrate typical create, read, update, and delete \(CRUD\) operations\. The example creates a sample `Book` instance and saves it to the `ProductCatalog` table\. It then retrieves the book item and updates its `ISBN` and `Authors` properties\. Note that the update replaces the existing authors list\. Finally, the example deletes the book item\.

For more information about the `ProductCatalog` table used in this example, see [Creating Tables and Loading Data for Code Examples in DynamoDB](SampleData.md)\. For step\-by\-step instructions to test the following example, see [\.NET Code Examples](CodeSamples.DotNet.md)\.

**Note**  
 The following example doesn't work with \.NET core because it doesn't support synchronous methods\. For more information, see [AWS Asynchronous APIs for \.NET](https://docs.aws.amazon.com/sdk-for-net/v3/developer-guide/sdk-net-async-api.html)\.

**Example**  

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
using System;
using System.Collections.Generic;
using Amazon.DynamoDBv2;
using Amazon.DynamoDBv2.DataModel;
using Amazon.Runtime;

namespace com.amazonaws.codesamples
{
    class HighLevelItemCRUD
    {
        private static AmazonDynamoDBClient client = new AmazonDynamoDBClient();

        static void Main(string[] args)
        {
            try
            {
                DynamoDBContext context = new DynamoDBContext(client);
                TestCRUDOperations(context);
                Console.WriteLine("To continue, press Enter");
                Console.ReadLine();
            }
            catch (AmazonDynamoDBException e) { Console.WriteLine(e.Message); }
            catch (AmazonServiceException e) { Console.WriteLine(e.Message); }
            catch (Exception e) { Console.WriteLine(e.Message); }
        }

        private static void TestCRUDOperations(DynamoDBContext context)
        {
            int bookID = 1001; // Some unique value.
            Book myBook = new Book
            {
                Id = bookID,
                Title = "object persistence-AWS SDK for.NET SDK-Book 1001",
                ISBN = "111-1111111001",
                BookAuthors = new List<string> { "Author 1", "Author 2" },
            };

            // Save the book.
            context.Save(myBook);
            // Retrieve the book.
            Book bookRetrieved = context.Load<Book>(bookID);

            // Update few properties.
            bookRetrieved.ISBN = "222-2222221001";
            bookRetrieved.BookAuthors = new List<string> { " Author 1", "Author x" }; // Replace existing authors list with this.
            context.Save(bookRetrieved);

            // Retrieve the updated book. This time add the optional ConsistentRead parameter using DynamoDBContextConfig object.
            Book updatedBook = context.Load<Book>(bookID, new DynamoDBContextConfig
            {
                ConsistentRead = true
            });

            // Delete the book.
            context.Delete<Book>(bookID);
            // Try to retrieve deleted book. It should return null.
            Book deletedBook = context.Load<Book>(bookID, new DynamoDBContextConfig
            {
                ConsistentRead = true
            });
            if (deletedBook == null)
                Console.WriteLine("Book is deleted");
        }
    }

    [DynamoDBTable("ProductCatalog")]
    public class Book
    {
        [DynamoDBHashKey] //Partition key
        public int Id
        {
            get; set;
        }
        [DynamoDBProperty]
        public string Title
        {
            get; set;
        }
        [DynamoDBProperty]
        public string ISBN
        {
            get; set;
        }
        [DynamoDBProperty("Authors")] //String Set datatype
        public List<string> BookAuthors
        {
            get; set;
        }
    }
}
```