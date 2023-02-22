# Viewing data access patterns<a name="workbench.Visualizer.Facets"></a>

In NoSQL Workbench, *facets* represent an application's different data access patterns for Amazon DynamoDB\. Facets give you a way to view a subset of the data in a table, without having to see records that don't meet the constraints of the facet\. Facets are considered a visual data modeling tool, and don't exist as a usable construct in DynamoDB, as they are purely an aid to modeling of access patterns\. 

**Import sample data model**

1. On the left, choose **Amazon DynamoDB**\.

1. In the Sample data models section, hover your pointer over Music Library Data Model and choose **Import**\.  
![\[Console screenshot showing import of sample data.\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/workbench/NoSQLWorkbenchImportSampleData.png)

1. In the navigation pane on the left side, choose the **visualizer** icon\.  
![\[Console screenshot showing the visualizer icon.\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/workbench/VisualizerChoose.png)

1. Choose the Songs table to expand it\. You'll be shown an aggregate view of your data\.  
![\[Console screenshot showing aggregate data and the expanded Songs table.\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/workbench/NoSQLWorkbenchAggregateView.png)

1. Choose **Facets** drop\-down arrow to expand the available facets\.

1. Choose the SongDetails facet to visualize the data with the SongDetails facet applied\.  
![\[Console screenshot showing the expanded table.\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/workbench/NoSQLWorkbenchExpandSongsTable.png)

![\[Console screenshot showing the facets in a data model.\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/workbench/NoSQLWorkbenchFacetView.png)

You can also edit the facet definitions using the Data Modeler\. For more information, see [Editing an existing data model](workbench.Modeler.Edit.md)\.