# Considerations When Changing Read/Write Capacity Mode<a name="switching.capacitymode"></a>

 You can switch between read/write capacity modes once every 24 hours\. Consider the following when updating your read/write capacity mode in Amazon DynamoDB\.

## Managing Capacity<a name="switching.capacity"></a>

 When you update a table from provisioned to on\-demand mode, you don't need to specify how much read and write throughput you expect your application to perform\. 

Consider the following when you update a table from on\-demand to provisioned mode: 
+  If you're using the AWS Management Console, the console estimates initial provisioned capacity values based on the consumed read and write capacity of your table and global secondary indexes over the past 30 minutes\. To override these recommended values, choose `Override recommended values`\. 
+  If you're using the AWS CLI or AWS SDK, choose the right provisioned capacity settings of your table and global secondary indexes by using Amazon CloudWatch to look at your historical consumption \(`ConsumedWriteCapacityUnits` and `ConsumedReadCapacityUnits` metrics\) to determine the new throughput settings\. 
**Note**  
 If you're switching a global table to provisioned mode, look at the maximum consumption across all your regional replicas for base tables and global secondary indexes when determining the new throughput settings\. 

## Managing Auto Scaling<a name="switching.autoscaling"></a>

 When you update a table from provisioned to on\-demand mode:
+  If you're using the console, all of your auto scaling settings \(if any\) will be deleted\. 
+  If you're using the AWS CLI or AWS SDK, all of your auto scaling settings will be preserved\. These settings can be applied when you update your table to provisioned billing mode again\. 

 When you update a table from on\-demand to provisioned mode: 
+  If you're using the console, DynamoDB recommends enabling auto scaling with the following defaults: 
  +  Target utilization: 70% 
  +  Minimum provisioned capacity: 5 units 
  +  Maximum provisioned capacity: The Region maximum 
+  If you're using the AWS CLI or SDK, your previous auto scaling settings \(if any\) are preserved\. 