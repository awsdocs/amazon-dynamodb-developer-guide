# Evaluate your table class selection<a name="CostOptimization_TableClass"></a>

This section provides an overview of how to select the appropriate table class for your DynamoDB table\. With the launch of the Standard Infrequent\-Access \(Standard\-IA\) table class, you now have the ability to optimize a table for either lower storage cost or lower throughput cost\.

**Topics**
+ [What table classes are available](#CostOptimization_TableClass_Overview)
+ [When to select the DynamoDB Standard table class](#CostOptimization_TableClass_Standard)
+ [When to select DynamoDB Standard\-IA table class](#CostOptimization_TableClass_StandardIA)
+ [Additional factors to consider when choosing a table class](#CostOptimization_TableClass_AdditionalFactors)

## What table classes are available<a name="CostOptimization_TableClass_Overview"></a>

When you create a DynamoDB Table, you must select either DynamoDB Standard or DynamoDB Standard\-IA for the table class\. The table class can be changed twice in a 30\-day period, so you can always change it in the future\. Selecting either table class has no effect on table performance, availability, reliability, or durability\.

![\[Image showing the two different options when creating a DynamoDB table: Standard and Standard-Infrequent Access.\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/CostOptimization/TableClassOptions.png)

**Standard table class**  
The Standard table class is the default option for new tables\. This option maintains the original billing balance of DynamoDB which offers a balance of throughput and storage costs for tables with frequently accessed data\.

**Standard\-IA table class**  
The Standard\-IA table class offers a lower storage cost \(\~60% lower\) for workloads that require long\-term storage of data with infrequent updates or reads\. Since the class is optimized for infrequent access, reads and writes will be billed at a slightly higher cost \(\~25% higher\) than the Standard table class\.

## When to select the DynamoDB Standard table class<a name="CostOptimization_TableClass_Standard"></a>

DynamoDB Standard table class is best suited for tables whose storage cost is approximately 50% or less of the overall monthly cost of the table\. This cost balance is indicative of a workload that regularly accesses or updates items already stored within DynamoDB\.

## When to select DynamoDB Standard\-IA table class<a name="CostOptimization_TableClass_StandardIA"></a>

DynamoDB Standard\-IA table class is best suited for tables whose storage cost is approximately 50% or more of the overall monthly cost of the table\. This cost balance is indicative of a workload that creates or reads fewer items per month than it keeps in storage\.

 A common use for the Standard\-IA table class is moving less frequently accessed data to individual Standard\-IA tables\. For further information, see [ Optimizing the storage costs of your workloads with Amazon DynamoDB Standard\-IA table class](https://aws.amazon.com/blogs/database/optimize-the-storage-costs-of-your-workloads-with-amazon-dynamodb-standard-ia-table-class/)\.

## Additional factors to consider when choosing a table class<a name="CostOptimization_TableClass_AdditionalFactors"></a>

When deciding between the two table classes, there are some additional factors worth considering as part of your decision\.

**Reserved capacity**  
Purchasing reserved capacity for tables using the Standard\-IA table class is currently not supported\. When transitioning from a Standard table with reserved capacity to a Standard\-IA table without reserved capacity, you may not see a cost benefit\.