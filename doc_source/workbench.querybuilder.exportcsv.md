# Exporting data to a CSV file<a name="workbench.querybuilder.exportcsv"></a>

You can export the results of a query from Operation Builder to a CSV file\. This enables you to load the data into a spreadsheet or process it using your preferred programming language\.

**Exporting to CSV**

1. In the Operation Builder, run an operation of your choice, such as a Scan or Query\.
**Note**  
The operation may a PartiQL statement or an API operation, but it cannot be a batch or transaction operation\.
For paginated operations, only the first page of results will be exported\.

1. In the **Results** panel, choose **Export to CSV**\.  
![\[Console screenshot showing the Export to CSV button.\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/workbench/QueryBuilderExportToCSV.png)

1. Choose a filename and location for your CSV file and select **Save**\.