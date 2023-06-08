# Creating a new data model<a name="workbench.Modeler.CreateNew"></a>

Follow these steps to create a new data model in Amazon DynamoDB using NoSQL Workbench\.

**To create a new data model**

1.  Open NoSQL Workbench, and in the navigation pane on the left side, choose the **Data modeler** icon\.   
![\[Console screenshot showing the data modeler icon in DynamoDB.\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/workbench/DesignerChoose.png)

1. Choose **Create data model**\.  
![\[Console screenshot showing the Create data model button.\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/workbench/DesignerCreateModel.png)

    **Create data model** has two choices: Make model from scratch and Start from a template\.   
![\[Console screenshot showing the Create data model screen.\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/workbench/CreateDataModel-1.png)

------
#### [ Make model from scratch ]

    To make a model from scratch, enter a name, author, and description for the data model\. Choose **Create** when finished\. 

![\[Console screenshot showing create model from scratch\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/workbench/CreateDataModelFromScratch.png)

------
#### [ Start from a template ]

    Starting from a template lets you choose a sample model to start from\. Choose **More templates** to see more template options\. Choose **Select** for the template that you want to use\. 

    Enter a data model name, author, and description for the template you selected\. You can choose between **Schema only** and **Schema with sample data**\. 
   +  **Schema only** creates an empty data model with the primary key \(partition and sort key\) and other attributes\. 
   +  **Schema with sample data** will create a data model complete with sample data for the primary key \(partition and sort key\) and other attributes\. 

    When this information is complete, choose **Create** to create the model\. 

![\[Console screenshot showing create model from template\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/workbench/CreateModelFromTemplate-2.png)

------

1. With the model created, choose **Add table**\.  
![\[Console screenshot showing the add table button.\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/workbench/DesignerAddTableButton.png)

    For more information about tables, see [Working with tables in DynamoDB](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/WorkingWithTables.html)\. 

1. Specify the following:
   +  **Table name** – Enter a unique name for the table\. 
   +  **Partition key** – Enter a partition key name and specify its type\. 
   +  If you want to add a sort key: 

     1.  Select **Add sort key**\. 

     1.  Specify the sort key name and its type\.   
![\[Console screenshot showing the table name and partition key boxes and the add sort key check box.\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/workbench/DesignerCreateTable.png)
**Note**  
 To learn more about primary key design, designing and using partition keys effectively, and using sort keys, see the following:   
 [Primary key](HowItWorks.CoreComponents.md#HowItWorks.CoreComponents.PrimaryKey) 
 [Best practices for designing and using partition keys effectively](bp-partition-key-design.md) 
 [Best practices for using sort keys to organize data](bp-sort-keys.md) 

1.  To add other attributes, do the following for each attribute: 

   1.  Choose **Add other attribute**\. 

   1.  Specify the attribute name and type\.   
![\[Console screenshot showing the add other attribute button.\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/workbench/DesignerAddAttribute.png)

1.  Add a facet: 
**Note**  
 *Facets* represent an application's different data access patterns for DynamoDB\. To learn more about facets, see [Viewing data access patterns](workbench.Visualizer.Facets.md)\. 
   +  Select **Add facets**\. 
   +  Choose **Add facet**\.   
![\[Console screenshot showing the add facets button and the add facet button.\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/workbench/DesignerAddFacet.png)
   + Specify the following:
     + The **Facet name**\.
     + A **Partition key alias**\.
     + A **Sort key alias**\.
     + Choose the **Other attributes** that are part of this facet\.

   Choose **Add facet**\.  
![\[Console screenshot showing the facet details and the add facet button.\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/workbench/DesignerAddFacetDetails.png)

   Repeat this step if you want to add more facets\.

1. If you want to add a global secondary index, choose **Add global secondary index**\.

   Specify the **Global secondary index name**, **Partition key** attribute, and **Projection type**\.  
![\[Console screenshot showing the add GSI button.\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/workbench/DesignerAddIndexes.png)

   For more information about working with global secondary indexes in DynamoDB, see [Global secondary indexes](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/GSI.html)\.

1. By default, your table will use provisioned capacity mode with auto scaling enabled on both read and write capacity\. If you want to change these settings, clear **Default settings** under **Capacity settings**\.  
![\[Console screenshot showing the Capacity settings heading with "Default settings" selected.\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/workbench/DesignerCapacityCheckbox.png)

   Select your desired capacity mode, read and write capacity, and auto scaling IAM role \(if applicable\)\.  
![\[Console screenshot showing the table capacity settings\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/workbench/DesignerCapacitySettings.png)

   For more information about DynamoDB capacity settings, see [Read/write capacity mode](HowItWorks.ReadWriteCapacityMode.md)\.

1.  Choose **Add table definition**\.  
![\[Console screenshot showing the add table definition button.\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/workbench/designerAddTabledefinitionbutton.png)

For more information about the `CreateTable` API operation, see [CreateTable](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_CreateTable.html) in the *Amazon DynamoDB API Reference*\.