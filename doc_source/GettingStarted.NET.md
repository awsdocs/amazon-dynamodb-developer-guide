# Getting Started with \.NET and DynamoDB<a name="GettingStarted.NET"></a>

In this tutorial, you use the AWS SDK for \.NET to write simple programs to perform the following Amazon DynamoDB operations:
+ Create a table named `Movies` using a utility program written in C\#, and load sample data in JSON format\.
+ Perform create, read, update, and delete operations on the table\.
+ Run simple queries\.

 The DynamoDB module of the AWS SDK for \.NET offers several programming models for different use cases\. In this exercise, the C\# code uses the document model, which provides a level of abstraction that is often convenient\. It also uses the low\-level API, which handles nested attributes more effectively\. 

For information about the document model API, see [\.NET: Document Model](DotNetSDKMidLevel.md)\. For information about the low\-level API, see [Working with DynamoDB Tables in \.NET](LowLevelDotNetWorkingWithTables.md)\. 

**Topics**
+ [\.NET and DynamoDB Tutorial Prerequisites](GettingStarted.NET.Prereqs.md)
+ [Step 1: Create a DynamoDB Client](GettingStarted.NET.01.md)
+ [Step 2: Create a DynamoDB Table Using the Low\-Level API](GettingStarted.NET.02.md)
+ [Step 3: Load Sample Data into the DynamoDB Table](GettingStarted.NET.03.md)
+ [Step 4: Add a Movie to the DynamoDB Table](GettingStarted.NET.04.md)
+ [Step 5: Read and Display a Record from the DynamoDB Table](GettingStarted.NET.05.md)
+ [Step 6: Update the New Movie Record in the DynamoDB Table](GettingStarted.NET.06.md)
+ [Step 7: Conditionally Delete](GettingStarted.NET.07.md)
+ [Step 8: Query a DynamoDB Table with \.NET](GettingStarted.NET.08.md)
+ [Step 9: Scan the Movies Table with \.NET](GettingStarted.NET.09.md)
+ [Step 10: Delete the Movies Table with \.NET](GettingStarted.NET.10.md)