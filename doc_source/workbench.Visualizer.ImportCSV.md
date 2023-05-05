# Importing sample data from a CSV file<a name="workbench.Visualizer.ImportCSV"></a>

If you have preexisting sample data in a CSV file, you can import it into NoSQL Workbench\. This enables you to quickly populate your model with sample data without having to enter it line by line\.

The column names in the CSV file must match the attribute names in your data model, but they do not need to be in the same order\. For example, if your data model has attributes called `LoginAlias`, `FirstName`, and `LastName`, your CSV columns could be `LastName`, `FirstName`, and `LoginAlias`\.

Data import from a CSV file is limited to 150 rows at a time\.

**To import data from a CSV file into NoSQL Workbench**

1. In the navigation pane on the left side, choose the **visualizer** icon\.  
![\[Console screenshot showing the visualizer icon.\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/workbench/VisualizerChoose.png)

1. In the visualizer, choose **Update** next to the table name\.  
![\[Console screenshot showing the update button.\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/workbench/VisualizerUpdate.png)

1. Choose **Import CSV file**\.  
![\[Console screenshot showing the Import CSV file button.\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/workbench/VisualizerImportCSV.png)

1. Select your CSV file and choose **Open**\. The data in the CSV file will be appended to your table\.
**Note**  
If your CSV file contains one or more rows that have the same keys as items already in your table, you will have the option of overwriting the existing items or appending them to the end of the table\. If you choose to append the items, the suffix "\-Copy" will be added to each duplicate item's key to differentiate the duplicate items from the items that were already in the table\.