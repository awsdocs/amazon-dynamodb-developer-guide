# Amazon S3 import formats for DynamoDB<a name="S3DataImport.Format"></a>

DynamoDB can import data in three formats: CSV, DynamoDB JSON, and Amazon Ion\.

**Topics**
+ [CSV](#S3DataImport.Requesting.Formats.CSV)
+ [DynamoDB Json](#S3DataImport.Requesting.Formats.DDBJson)
+ [Amazon Ion](#S3DataImport.Requesting.Formats.Ion)

## CSV<a name="S3DataImport.Requesting.Formats.CSV"></a>

A file in CSV format consists of multiple items delimited by newlines\. By default, DynamoDB interprets the first line of an import file as the header and expects columns to be delimited by commas\. You can also define headers that will be applied, as long as they match the number of columns in the file\. If you define headers explicitly, the first line of the file will be imported as values\. 

**Note**  
When importing from CSV files, all columns other than the hash range and keys of your base table and secondary indexes are imported as DynamoDB strings\.

## DynamoDB Json<a name="S3DataImport.Requesting.Formats.DDBJson"></a>

A file in DynamoDB JSON format consists of multiple Item objects\. Each individual object is in DynamoDB’s standard marshalled JSON format, and newlines are used as item delimiters\. As an added feature, exports from point in time are supported as an import source by default\.

**Note**  
In the following example, a single item from a file in DynamoDB JSON format has been formatted on multiple lines for the sake of readability\.

```
{
    "Item":{
        "Authors":{
            "SS":[
                "Author1",
                "Author2"
            ]
        },
        "Dimensions":{
            "S":"8.5 x 11.0 x 1.5"
        },
        "ISBN":{
            "S":"333-3333333333"
        },
        "Id":{
            "N":"103"
        },
        "InPublication":{
            "BOOL":false
        },
        "PageCount":{
            "N":"600"
        },
        "Price":{
            "N":"2000"
        },
        "ProductCategory":{
            "S":"Book"
        },
        "Title":{
            "S":"Book 103 Title"
        }
    }
}
```

## Amazon Ion<a name="S3DataImport.Requesting.Formats.Ion"></a>

[Amazon Ion](https://amzn.github.io/ion-docs/) is a richly\-typed, self\-describing, hierarchical data serialization format built to address rapid development, decoupling, and efficiency challenges faced every day while engineering large\-scale, service\-oriented architectures\.

When you import data in Ion format, the Ion datatypes are mapped to DynamoDB datatypes in the new DynamoDB table\.


|  | Ion to DynamoDB datatype conversion | B | 
| --- | --- | --- | 
| `1` | `Ion Data Type` | `DynamoDB Representation` | 
| `2` | `string` | `String (s)` | 
| `3` | `bool` | `Boolean (BOOL)` | 
| `4` | `decimal` | `Number (N)` | 
| `5` | `blob` | `Binary (B)` | 
| `6` | `list (with type annotation $dynamodb_SS, $dynamodb_NS, or $dynamodb_BS)` | `Set (SS, NS, BS)` | 
| `7` | `list` | `List` | 
| `8` | `struct` | `Map` | 

Items in an Ion file are delimited by newlines\. Each line begins with an Ion version marker, followed by an item in Ion format\.

**Note**  
In the following example, an item from an Ion\-formatted file has been formatted on multiple lines for the sake of readability\.

```
$ion_1_0 {
    Item:{
        Authors:$dynamodb_SS::["Author1","Author2"],
        Dimensions:"8.5 x 11.0 x 1.5",
        ISBN:"333-3333333333",
        Id:103.,
        InPublication:false,
        PageCount:6d2,
        Price:2d3,
        ProductCategory:"Book",
        Title:"Book 103 Title"
    }
}
```