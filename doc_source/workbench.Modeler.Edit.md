# Editing an existing data model<a name="workbench.Modeler.Edit"></a>

**To edit an existing model**

1. In NoSQL Workbench, in the navigation pane on the left side, choose the **Data modeler** button\.  
![\[Console screenshot showing the data modeler button.\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/workbench/DesignerChoose.png)

1. Choose the table that you want to edit\.

   Choose **Edit**\.  
![\[Console screenshot showing the Edit link in the data modeler.\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/workbench/DesignerEditModel.png)

1. Make the needed edits, and then choose **Save edits**\.  
![\[Console screenshot showing the save edits button.\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/workbench/Designersaveeditsbutton.png)

**To manually edit an existing model and add a facet**

1. Export your model\. For more information, see [Exporting a data model](workbench.Modeler.ExportModel.md)\.

1. Open the exported file in an editor\.

1. Locate the `DataModel` Object for the table that you want to create a facet for\. 

   Add a `TableFacets` array representing all the facets for the table\. 

   For each facet, add an object to the `TableFacets` array\. Each array element has the following properties: 
   + `FacetName` – A name for your facet\. This value must be unique across the model\. 
   + `PartitionKeyAlias` – A friendly name for the table's partition key\. This alias is displayed when you view the facet in NoSQL Workbench\. 
   + `SortKeyAlias` – A friendly name for the table's sort key\. This alias is displayed when you view the facet in NoSQL Workbench\. This property is not needed if the table has no sort key defined\.
   + `NonKeyAttributes` – An array of attribute names that are needed for the access pattern\. These names must map to the attribute names that are defined for the table\.

   ```
   {
     "ModelName": "Music Library Data Model",
     "DataModel": [
       {
         "TableName": "Songs",
         "KeyAttributes": {
           "PartitionKey": {
             "AttributeName": "Id",
             "AttributeType": "S"
           },
           "SortKey": {
             "AttributeName": "Metadata",
             "AttributeType": "S"
           }
         },
         "NonKeyAttributes": [
           {
             "AttributeName": "DownloadMonth",
             "AttributeType": "S"
           },
           {
             "AttributeName": "TotalDownloadsInMonth",
             "AttributeType": "S"
           },
           {
             "AttributeName": "Title",
             "AttributeType": "S"
           },
           {
             "AttributeName": "Artist",
             "AttributeType": "S"
           },
           {
             "AttributeName": "TotalDownloads",
             "AttributeType": "S"
           },
           {
             "AttributeName": "DownloadTimestamp",
             "AttributeType": "S"
           }
         ],
         "TableFacets": [
           {
             "FacetName": "SongDetails",
             "KeyAttributeAlias": {
               "PartitionKeyAlias": "SongId",
               "SortKeyAlias": "Metadata"
             },
             "NonKeyAttributes": [
               "Title",
               "Artist",
               "TotalDownloads"
             ]
           },
           {
             "FacetName": "Downloads",
             "KeyAttributeAlias": {
               "PartitionKeyAlias": "SongId",
               "SortKeyAlias": "Metadata"
             },
             "NonKeyAttributes": [
               "DownloadTimestamp"
             ]
           }
         ]
       }
     ]
   }
   ```

1. You can now import the modified model into NoSQL Workbench\. For more information, see [Importing an existing data model](workbench.Modeler.ImportExisting.md)\.