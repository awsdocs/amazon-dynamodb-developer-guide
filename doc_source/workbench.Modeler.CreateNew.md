# Creating a New Data Model<a name="workbench.Modeler.CreateNew"></a>

Follow these steps to create a new data model in Amazon DynamoDB using NoSQL Workbench\.

**To create a new data model**

1. Open NoSQL Workbench, and in the navigation pane on the left side, choose the **Data modeler** icon\.  
![\[Console screenshot showing the data modeler icon in DynamoDB.\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/workbench/DesignerChoose.png)

1. Choose **Create data model**\.  
![\[Console screenshot showing the Create data model button.\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/workbench/DesignerCreateModel.png)

1. Enter a name for the data model, and then choose **Create**\.  
![\[Console screenshot showing the data model name and create button.\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/workbench/DesignerCreateModelName.png)

1. On the newly created model, choose **Open**\.  
![\[Console screenshot showing the open button in the data modeler.\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/workbench/DesignerOpenDataModel.png)

1. Choose **Add table**\.  
![\[Console screenshot showing the add table button.\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/workbench/DesignerAddTableButton.png)

   For more information about tables, see [Working with Tables in DynamoDB](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/WorkingWithTables.html)\.

1. Specify the following:
   + **Table name** — Enter a unique name for the table\.
   + **Partition key** — Enter a partition key name and specify its type\.
   + If you want to add a sort key:

     1. Select **Add sort key**\.

     1. Specify the sort key name and its type\.  
![\[Console screenshot showing the table name and partition key boxes and the add sort key checkbox.\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/workbench/DesignerCreateTable.png)

1. To add other attributes, do the following for each attribute:

   1. Choose **Add other attribute**\.

   1. Specify the attribute name and type\.   
![\[Console screenshot showing the add other attribute button.\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/workbench/DesignerAddAttribute.png)

1. Add a facet:
**Note**  
*Facets* represent an application's different data access patterns for DynamoDB\.
   + Select **Add facets**\.
   + Choose **Add facet**\.  
![\[Console screenshot showing the add facets button and the add facet button.\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/workbench/DesignerAddFacet.png)
   + Specify the following:
     + The **Facet name**\.
     + A **Partition key alias**\.
     + A **Sort key alias**\.
     + Choose the **Other attributes** that are part of this facet\.

   Choose **Add facet**\.  
![\[Console screenshot showing the facet details and the add facet button.\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/workbench/DesignerAddFacetDetails.png)

   Repeat step 8 if you want to add more facets\.

1. If you want to add a global secondary index, choose **Add global secondary index**\.

   Specify the **Global secondary index name**, **Partition key** attribute, and **Projection type**\.  
![\[Console screenshot showing the add GSI button.\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/workbench/DesignerAddIndexes.png)

   For more information about working with global secondary indexes in DynamoDB, see [Global Secondary Indexes](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/GSI.html)\.

1.  Choose **Add table definition**\.  
![\[Console screenshot showing the add table definition button.\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/workbench/designerAddTabledefinitionbutton.png)

For more information about the `CreateTable` API operation, see [CreateTable](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_CreateTable.html) in the *Amazon DynamoDB API Reference*\.