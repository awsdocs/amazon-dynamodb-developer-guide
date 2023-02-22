# Using the DynamoDB Well\-Architected Lens to optimize your DynamoDB workload<a name="bp-wal"></a>

This section describes the Amazon DynamoDB Well\-Architected Lens, a collection of design principles and guidance for designing well\-architected DynamoDB workloads\.

The AWS [Well\-Architected Framework](https://aws.amazon.com/architecture/well-architected/) helps cloud architects build secure, high\-performing, resilient, and efficient infrastructure for a variety of applications and workloads\. Built around six pillars—operational excellence, security, reliability, performance efficiency, cost optimization, and sustainability—AWS Well\-Architected provides a consistent approach for customers and partners to evaluate architectures and implement scalable designs\.

The AWS [Well\-Architected Lenses](https://docs.aws.amazon.com/wellarchitected/latest/userguide/lenses.html) extend the guidance offered by AWS Well\-Architected to specific industry and technology domains\. The Amazon DynamoDB Well\-Architected Lens focuses on DynamoDB workloads\. It provides best practices, design principles and questions to assess and review a DynamoDB workload\. Completing an Amazon DynamoDB Well\-Architected Lens review will provide you with education and guidance around recommended design principles as it relates to each of the AWS Well\-Architected pillars\. This guidance is based on our experience working with customers across various industries, segments, sizes and geographies\.

 As a direct outcome of the Well\-Architected Lens review, you will receive a summary of actionable recommendations to optimize and improve your DynamoDB workload\. 

## Conducting the Amazon DynamoDB Well\-Architected Lens review<a name="bp-wal-conducting"></a>

The DynamoDB Well\-Architected Lens review is usually performed by an AWS Solutions Architect together with the customer, but can also be performed by the customer as a self\-service\. While we recommend reviewing all six of the Well\-Architected Pillars as part of the Amazon DynamoDB Well\-Architected Lens, you can also decide to prioritize your focus on one or more pillars first\.

Additional information and instructions for conducting an Amazon DynamoDB Well\-Architected Lens review are available in [this video ](https://youtu.be/mLAUvJYvBjA) and the [DynamoDB Well\-Architected Lens GitHub page ](https://github.com/aws-samples/custom-lens-wa-hub/tree/main/DynamoDB)\.

## The pillars of the Amazon DynamoDB Well\-Architected Lens<a name="bp-wal-pillars"></a>

The Amazon DynamoDB Well\-Architected Lens is built around six pillars:

**Performance efficiency pillar**

The performance efficiency pillar includes the ability to use computing resources efficiently to meet system requirements, and to maintain that efficiency as demand changes and technologies evolve\.

The primary DynamoDB design principles for this pillar revolve around [modeling the data ](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/bp-relational-modeling.html), [choosing partition keys ](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/HowItWorks.Partitions.html#HowItWorks.Partitions.SimpleKey) and [sort keys ](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/HowItWorks.Partitions.html#HowItWorks.Partitions.CompositeKey), and [defining secondary indexes ](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/bp-indexes.html) based on the application access patterns\. Additional considerations include choosing the optimal throughput mode for the workload, AWS SDK tuning and, when appropriate, using an optimal caching strategy\. To learn more about these design principles, watch this [deep dive video ](https://youtu.be/PuCIy5Weyi8) about the performance efficiency pillar of the DynamoDB Well\-Architected Lens\.

**Cost optimization pillar**

The cost optimization pillar focuses on avoiding unnecessary costs\. 

Key topics include understanding and controlling where money is being spent, selecting the most appropriate and right number of resource types, analyzing spend over time, designing your data models to optimize the cost for application\-specific access patterns, and scaling to meet business needs without overspending\.

The key cost optimization design principles for DynamoDB revolve around choosing the most appropriate capacity mode and table class for your tables and avoiding over\-provisioning capacity by either using the on\-demand capacity mode, or provisioned capacity mode with autoscaling\. Additional considerations include efficient data modeling and querying to reduce the amount of consumed capacity, reserving portions of the consumed capacity at discounted price, minimizing item size, identifying and removing unused resources and using TTL to automatically delete aged\-out data at no cost\. To learn more about these design principles, watch this [deep dive video](https://youtu.be/iuI0HUuw6Jg) about the cost optimization pillar of the DynamoDB Well\-Architected Lens\.

See [Cost optimization](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/bp-cost-optimization.html) for additional information on cost optimization best practices for DynamoDB\.

**Operational excellence pillar**

The operational excellence pillar focuses on running and monitoring systems to deliver business value, and continually improving processes and procedures\. Key topics include automating changes, responding to events, and defining standards to manage daily operations\.

The main operational excellence design principles for DynamoDB include monitoring DynamoDB metrics through Amazon CloudWatch and AWS Config and automatically alert and remediate when predefined thresholds are breached, or non compliant rules are detected\. Additional considerations are defining DynamoDB resources via infrastructure as a code and leveraging tags for better organization, identification and cost accounting of your DynamoDB resources\. To learn more about these design principles, watch this [deep dive video ](https://youtu.be/41HUSL9tJa8) about the operational excellence pillar of the DynamoDB Well\-Architected Lens\.

**Reliability pillar**

The reliability pillar focuses on ensuring a workload performs its intended function correctly and consistently when it’s expected to\. A resilient workload quickly recovers from failures to meet business and customer demand\. Key topics include distributed system design, recovery planning, and how to handle change\. The operational excellence pillar focuses on running and monitoring systems to deliver business value, and continually improving processes and procedures\. Key topics include automating changes, responding to events, and defining standards to manage daily operations\.

The essential reliability design principles for DynamoDB revolve around choosing the backup strategy and retention based on your RPO and RTO requirements, using DynamoDB global tables for multi\-regional workloads, or cross\-region disaster recovery scenarios with low RTO, implementing retry logic with exponential backoff in the application by configuring and using these capabilities in the AWS SDK, and monitoring DynamoDB metrics through Amazon CloudWatch and automatically alerting and remediating when predefined thresholds are breached\. To learn more about these design principles, watch this [deep dive video ](https://youtu.be/8AoPBxVQYM8) about the reliability pillar of the DynamoDB Well\-Architected Lens\.

**Security pillar**

The security pillar focuses on protecting information and systems\. Key topics include confidentiality and integrity of data, identifying and managing who can do what with privilege management, protecting systems, and establishing controls to detect security events\.

The main security design principles for DynamoDB are encrypting data in transit with HTTPS, choosing the type of keys for data at rest encryption and defining the IAM roles and policies to authenticate, authorize and provide fine grain access to DynamoDB resources\. Additional considerations include auditing DynamoDB control plane and data plane operations through AWS CloudTrail\. To learn more about these design principles, watch this [deep dive video](https://youtu.be/G_BUsA22kJ8) about the security pillar of the DynamoDB Well\-Architected Lens\.

See [Security](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/security.html) for additional information on security for DynamoDB\.

**Sustainability pillar**

The sustainability pillar focuses on minimizing the environmental impacts of running cloud workloads\. Key topics include a shared responsibility model for sustainability, understanding impact, and maximizing utilization to minimize required resources and reduce downstream impacts\.

The main sustainability design principles for DynamoDB include identifying and removing unused DynamoDB resources, avoiding over\-provisioning though the usage of on\-demand capacity mode or provisioned capacity\-mode with autoscaling, efficient querying to reduce the amount of capacity being consumed and reduction of the storage footprint by compressing data and by deleting aged\-out data through the use of TTL\. To learn more about these design principles, watch this [deep dive video ](https://youtu.be/fAfYms7u3EE) about the sustainability pillar of the DynamoDB Well\-Architected Lens\.