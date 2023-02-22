# DynamoDB table export output format<a name="S3DataExport.Output"></a>

A DynamoDB table export includes two manifest files in addition to the files containing your table data\. These files are all saved in the Amazon S3 bucket that you specify in your [export request](S3DataExport_Requesting.md)\. The following sections describe the format and contents of each output object\.

**Topics**
+ [Manifest files](#S3DataExport.Output_Manifest)
+ [Data objects](#S3DataExport.Output_Data)

## Manifest files<a name="S3DataExport.Output_Manifest"></a>

DynamoDB creates two manifest files, along with their checksum files, in the specified S3 bucket for each export request\.

```
export-prefix/AWSDynamoDB/ExportId/manifest-summary.json
export-prefix/AWSDynamoDB/ExportId/manifest-summary.checksum
export-prefix/AWSDynamoDB/ExportId/manifest-files.json
export-prefix/AWSDynamoDB/ExportId/manifest-files.checksum
```

You choose an **export\-prefix** when you request a table export\. This helps you keep files in the destination S3 bucket organized\. The **ExportId** is a unique token to ensure that multiple exports to the same S3 bucket and `export-prefix` don't overwrite each other\.

This export also creates at least 1 file per partition\. All of the items in each file are from that particular partition's hashed keyspace\.

**Note**  
DynamoDB also creates an empty file named `_started` in the same directory as the manifest files\. This file verifies that the destination bucket is writable and that the export has begun\. It can safely be deleted\.

### The summary manifest<a name="S3DataExport.Output_Manifest_Summary"></a>

The `manifest-summary.json` file contains summary information about the export job\. Its format is as follows:

```
{
   "version": "2020-06-30",
   "exportArn": "arn:aws:dynamodb:us-east-1:123456789012:table/ProductCatalog/export/01234567890123-a1b2c3d4",
   "startTime": "2020-11-04T07:28:34.028Z",
   "endTime": "2020-11-04T07:33:43.897Z",
   "tableArn": "arn:aws:dynamodb:us-east-1:123456789012:table/ProductCatalog",
   "tableId": "12345a12-abcd-123a-ab12-1234abc12345",
   "exportTime": "2020-11-04T07:28:34.028Z",
   "s3Bucket": "ddb-productcatalog-export",
   "s3Prefix": "2020-Nov",
   "s3SseAlgorithm": "AES256",
   "s3SseKmsKeyId": null,
   "manifestFilesS3Key": "AWSDynamoDB/01345678901234-abc12345/manifest-files.json",
   "billedSizeBytes": 0,
   "itemCount": 8,
   "outputFormat": "DYNAMODB_JSON"
}
```

### The files manifest<a name="S3DataExport.Output_Manifest_Files"></a>

The `manifest-files.json` file contains information about the files that contain your exported table data\. The file is in [JSON lines](https://jsonlines.org/) format, meaning that newlines are used as item delimiters\. In the following example, the details of one data file from a files manifest are formatted on multiple lines for the sake of readability\.

```
{
   "itemCount": 8,
   "md5Checksum": "sQMSpEILNgoQmarvDFonGQ==",
   "etag": "af83d6f217c19b8b0fff8023d8ca4716-1",
   "dataFileS3Key": "AWSDynamoDB/11111111111111-22222222/data/33334444EXAMPLE.json.gz"
}
```

## Data objects<a name="S3DataExport.Output_Data"></a>

DynamoDB can export your table data in two formats: DynamoDB JSON and Amazon Ion\. Regardless of the format you choose, your data will be written to multiple compressed files named by the keys found in the `manifest-files.json` file\.

```
export-prefix/AWSDynamoDB/ExportId/data/bafybeiczss3yxay3o4abnabbb.json.gz
export-prefix/AWSDynamoDB/ExportId/data/gkes5o3lnrhoznhnkyax3hxvya.json.gz
```

### DynamoDB JSON<a name="S3DataExport.Output_Data_DDB-JSON"></a>

A table export in DynamoDB JSON format consists of multiple `Item` objects\. Each individual object is in DynamoDB's standard marshalled JSON format\.

When creating custom parsers for DynamoDB JSON export data, keep in mind that the format is [JSON lines](https://jsonlines.org/)\. This means that newlines are used as item delimiters\. Many AWS services, such as Athena and AWS Glue, will parse this format automatically\.

In the following example, a single item from a DynamoDB JSON export has been formatted on multiple lines for the sake of readability\.

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

### Amazon Ion<a name="S3DataExport.Output_Data_ION"></a>

[Amazon ion](http://amzn.github.io/ion-docs/) is a richly\-typed, self\-describing, hierarchical data serialization format built to address rapid development, decoupling, and efficiency challenges faced every day while engineering large\-scale, service\-oriented architectures\. DynamoDB supports exporting table data in Ion's [text format](http://amzn.github.io/ion-docs/docs/spec.html), which is a superset of JSON\.

When you export a table to Ion format, the DynamoDB datatypes used in the table are mapped to [Ion datatypes](http://amzn.github.io/ion-docs/docs/spec.html)\. DynamoDB sets use [Ion type annotations](http://amzn.github.io/ion-docs/docs/spec.html#annot) to disambiguate the datatype used in the source table\.


**DynamoDB to ion datatype conversion**  

| DynamoDB data type | Ion representation | 
| --- | --- | 
| String \(S\) | string | 
| Boolean \(BOOL\) | bool | 
| Number \(N\) | decimal | 
| Binary \(B\) | blob | 
| Set \(SS, NS, BS\) | list \(with type annotation $dynamodb\_SS, $dynamodb\_NS, or $dynamodb\_BS\) | 
| List | list | 
| Map | struct | 

Items in an Ion export are delimited by newlines\. Each line begins with an Ion version marker, followed by an item in Ion format\. In the following example, an item from an Ion export has been formatted on multiple lines for the sake of readability\.

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