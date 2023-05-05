# Importing an existing data model<a name="workbench.Modeler.ImportExisting"></a>

You can use NoSQL Workbench for Amazon DynamoDB to build a data model by importing and modifying an existing model\. You can import data models in either NoSQL Workbench model format or in [AWS CloudFormation JSON template format](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-dynamodb-table.html)\.

**To import a data model**

1. In NoSQL Workbench, in the navigation pane on the left side, choose the **Data modeler** icon\.  
![\[Console screenshot showing the data modeler icon.\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/workbench/DesignerChoose.png)

1. Hover your pointer over **Import data model**\.  
![\[Console screenshot showing the import data model button.\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/workbench/DesignerImportModel.png)

   In the dropdown list, choose whether the model you want to import is in NoSQL Workbench model format or CloudFormation JSON template format\. If you have an existing data model open in NoSQL Workbench, you'll have the option to import a CloudFormation template into the current model\.  
![\[Console screenshot showing the import model format dropdown menu.\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/workbench/DesignerImportModelDropdown.png)

1. Choose a model to import\.  
![\[Screenshot of file explorer with list of models to import.\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/workbench/DesignerImportModelSelect.png)

1. If the model you're importing is in CloudFormation template format, you'll see a list of tables to be imported and have an opportunity to specify a data model name, author, and description\.  
![\[Screenshot of import dialog for a AWS CloudFormation template.\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/workbench/DesignerImportCloudFormationModel.png)