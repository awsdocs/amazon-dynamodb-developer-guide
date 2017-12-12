# Time To Live<a name="TTL"></a>

Time To Live \(TTL\) for DynamoDB allows you to define when items in a table expire so that they can be automatically deleted from the database\. 

TTL is provided at no extra cost as a way to reduce storage usage and reduce the cost of storing irrelevant data without using provisioned throughput\. With TTL enabled on a table, you can set a timestamp for deletion on a per\-item basis, allowing you to limit storage usage to only those records that are relevant\.

TTL is useful if you have continuously accumulating data that loses relevance after a specific time period\. For example: session data, event logs, usage patterns, and other temporary data\. If you have sensitive data that must be retained only for a certain amount of time according to contractual or regulatory obligations, TTL helps you ensure that it is removed promptly and as scheduled\.

To get started with TTL:

1. Understand [Time To Live: How It Works](howitworks-ttl.md)\.

1. Read the [Before You Begin Using Time To Live](time-to-live-ttl-before-you-start.md) section\. 

1. Enable TTL on a specific table and choose the name of an attribute to hold the expiration timestamp\. Then, you can add or update items in the table with timestamps in the attribute you chose\. For more information, see [Enabling Time To Live](time-to-live-ttl-how-to.md)\.