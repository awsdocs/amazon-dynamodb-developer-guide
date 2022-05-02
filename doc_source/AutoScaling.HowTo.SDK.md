# Using the AWS SDK to Configure Auto Scaling on Amazon DynamoDB Tables<a name="AutoScaling.HowTo.SDK"></a>

In addition to using the AWS Management Console and the AWS Command Line Interface \(AWS CLI\), you can write applications that interact with Amazon DynamoDB auto scaling\. This section contains two Java programs that you can use to test this functionality:
+ `EnableDynamoDBAutoscaling.java`
+ `DisableDynamoDBAutoscaling.java`

## Enabling Application Auto Scaling for a Table<a name="AutoScaling.HowTo.SDK-enable"></a>

The following program shows an example of setting up an auto scaling policy for a DynamoDB table \(`TestTable`\)\. It proceeds as follows:
+ The program registers write capacity units as a scalable target for `TestTable`\. The range for this metric is between 5 and 10 write capacity units\.
+ After the scalable target is created, the program builds a target tracking configuration\. The policy seeks to maintain a 50 percent target ratio between consumed write capacity and provisioned write capacity\.
+ The program then creates the scaling policy, based on the target tracking configuration\.

**Note**  
When you manually remove a table or global table replica, you do not automatically remove any associated scalable targets, scaling policies, or CloudWatch alarms\.

The program requires that you provide an Amazon Resource Name \(ARN\) for a valid Application Auto Scaling service linked role\. \(For example: `arn:aws:iam::122517410325:role/AWSServiceRoleForApplicationAutoScaling_DynamoDBTable`\.\) In the following program, replace `SERVICE_ROLE_ARN_GOES_HERE` with the actual ARN\. 

```
/**
 * Copyright 2010-2019 Amazon.com, Inc. or its affiliates. All Rights Reserved.
 *
 * This file is licensed under the Apache License, Version 2.0 (the "License").
 * You may not use this file except in compliance with the License. A copy of
 * the License is located at
 *
 * http://aws.amazon.com/apache2.0/
 *
 * This file is distributed on an "AS IS" BASIS, WITHOUT WARRANTIES OR
 * CONDITIONS OF ANY KIND, either express or implied. See the License for the
 * specific language governing permissions and limitations under the License.
*/
package com.amazonaws.codesamples.autoscaling;

import com.amazonaws.services.applicationautoscaling.AWSApplicationAutoScalingClient;
import com.amazonaws.services.applicationautoscaling.AWSApplicationAutoScalingClientBuilder;
import com.amazonaws.services.applicationautoscaling.model.DescribeScalableTargetsRequest;
import com.amazonaws.services.applicationautoscaling.model.DescribeScalableTargetsResult;
import com.amazonaws.services.applicationautoscaling.model.DescribeScalingPoliciesRequest;
import com.amazonaws.services.applicationautoscaling.model.DescribeScalingPoliciesResult;
import com.amazonaws.services.applicationautoscaling.model.MetricType;
import com.amazonaws.services.applicationautoscaling.model.PolicyType;
import com.amazonaws.services.applicationautoscaling.model.PredefinedMetricSpecification;
import com.amazonaws.services.applicationautoscaling.model.PutScalingPolicyRequest;
import com.amazonaws.services.applicationautoscaling.model.RegisterScalableTargetRequest;
import com.amazonaws.services.applicationautoscaling.model.ScalableDimension;
import com.amazonaws.services.applicationautoscaling.model.ServiceNamespace;
import com.amazonaws.services.applicationautoscaling.model.TargetTrackingScalingPolicyConfiguration;

public class EnableDynamoDBAutoscaling {

    static AWSApplicationAutoScalingClient aaClient = (AWSApplicationAutoScalingClient) AWSApplicationAutoScalingClientBuilder.standard().build();

    public static void main(String args[]) {

	ServiceNamespace ns = ServiceNamespace.Dynamodb;
	ScalableDimension tableWCUs = ScalableDimension.DynamodbTableWriteCapacityUnits;
	String resourceID = "table/TestTable";

	// Define the scalable target
	RegisterScalableTargetRequest rstRequest = new RegisterScalableTargetRequest()
		.withServiceNamespace(ns)
		.withResourceId(resourceID)
		.withScalableDimension(tableWCUs)
		.withMinCapacity(5)
		.withMaxCapacity(10)
		.withRoleARN("SERVICE_ROLE_ARN_GOES_HERE");
	
	try {
	    aaClient.registerScalableTarget(rstRequest);
	} catch (Exception e) {
	    System.err.println("Unable to register scalable target: ");
	    System.err.println(e.getMessage());
	}

	// Verify that the target was created
	DescribeScalableTargetsRequest dscRequest = new DescribeScalableTargetsRequest()
		.withServiceNamespace(ns)
		.withScalableDimension(tableWCUs)
		.withResourceIds(resourceID);
	try {
	    DescribeScalableTargetsResult dsaResult = aaClient.describeScalableTargets(dscRequest);
	    System.out.println("DescribeScalableTargets result: ");
	    System.out.println(dsaResult);
	    System.out.println();
	} catch (Exception e) {
	    System.err.println("Unable to describe scalable target: ");
	    System.err.println(e.getMessage());
	}
	
	System.out.println();

	// Configure a scaling policy
	TargetTrackingScalingPolicyConfiguration targetTrackingScalingPolicyConfiguration = 
		new TargetTrackingScalingPolicyConfiguration()
		.withPredefinedMetricSpecification(
			new PredefinedMetricSpecification()
			.withPredefinedMetricType(MetricType. DynamoDBWriteCapacityUtilization))
		.withTargetValue(50.0)
		.withScaleInCooldown(60)
		.withScaleOutCooldown(60);

	// Create the scaling policy, based on your configuration
	PutScalingPolicyRequest pspRequest = new PutScalingPolicyRequest()
		.withServiceNamespace(ns)
		.withScalableDimension(tableWCUs)
		.withResourceId(resourceID)
		.withPolicyName("MyScalingPolicy")
		.withPolicyType(PolicyType.TargetTrackingScaling)
		.withTargetTrackingScalingPolicyConfiguration(targetTrackingScalingPolicyConfiguration);
	
	try {
	    aaClient.putScalingPolicy(pspRequest);
	} catch (Exception e) {
	    System.err.println("Unable to put scaling policy: ");
	    System.err.println(e.getMessage());
	}

	// Verify that the scaling policy was created
	DescribeScalingPoliciesRequest dspRequest = new DescribeScalingPoliciesRequest()
		.withServiceNamespace(ns)
		.withScalableDimension(tableWCUs)
		.withResourceId(resourceID);
	
	try {
	    DescribeScalingPoliciesResult dspResult = aaClient.describeScalingPolicies(dspRequest);
	    System.out.println("DescribeScalingPolicies result: ");
	    System.out.println(dspResult);
	} catch (Exception e) {
	    e.printStackTrace();
	    System.err.println("Unable to describe scaling policy: ");
	    System.err.println(e.getMessage());
	}

    }

}
```

## Disabling Application Auto Scaling for a Table<a name="AutoScaling.HowTo.SDK-disable"></a>

The following program reverses the previous process\. It removes the auto scaling policy and then deregisters the scalable target\.

```
/**
 * Copyright 2010-2019 Amazon.com, Inc. or its affiliates. All Rights Reserved.
 *
 * This file is licensed under the Apache License, Version 2.0 (the "License").
 * You may not use this file except in compliance with the License. A copy of
 * the License is located at
 *
 * http://aws.amazon.com/apache2.0/
 *
 * This file is distributed on an "AS IS" BASIS, WITHOUT WARRANTIES OR
 * CONDITIONS OF ANY KIND, either express or implied. See the License for the
 * specific language governing permissions and limitations under the License.
*/
package com.amazonaws.codesamples.autoscaling;

import com.amazonaws.services.applicationautoscaling.AWSApplicationAutoScalingClient;
import com.amazonaws.services.applicationautoscaling.model.DeleteScalingPolicyRequest;
import com.amazonaws.services.applicationautoscaling.model.DeregisterScalableTargetRequest;
import com.amazonaws.services.applicationautoscaling.model.DescribeScalableTargetsRequest;
import com.amazonaws.services.applicationautoscaling.model.DescribeScalableTargetsResult;
import com.amazonaws.services.applicationautoscaling.model.DescribeScalingPoliciesRequest;
import com.amazonaws.services.applicationautoscaling.model.DescribeScalingPoliciesResult;
import com.amazonaws.services.applicationautoscaling.model.ScalableDimension;
import com.amazonaws.services.applicationautoscaling.model.ServiceNamespace;

public class DisableDynamoDBAutoscaling {

    static AWSApplicationAutoScalingClient aaClient = new AWSApplicationAutoScalingClient();

    public static void main(String args[]) {

	ServiceNamespace ns = ServiceNamespace.Dynamodb;
	ScalableDimension tableWCUs = ScalableDimension.DynamodbTableWriteCapacityUnits;
	String resourceID = "table/TestTable";

	// Delete the scaling policy
	DeleteScalingPolicyRequest delSPRequest = new DeleteScalingPolicyRequest()
		.withServiceNamespace(ns)
		.withScalableDimension(tableWCUs)
		.withResourceId(resourceID)
		.withPolicyName("MyScalingPolicy");
	
	try {
	    aaClient.deleteScalingPolicy(delSPRequest);
	} catch (Exception e) {
	    System.err.println("Unable to delete scaling policy: ");
	    System.err.println(e.getMessage());
	}

	// Verify that the scaling policy was deleted
	DescribeScalingPoliciesRequest descSPRequest = new DescribeScalingPoliciesRequest()
		.withServiceNamespace(ns)
		.withScalableDimension(tableWCUs)
		.withResourceId(resourceID);
	
	try {
	    DescribeScalingPoliciesResult dspResult = aaClient.describeScalingPolicies(descSPRequest);
	    System.out.println("DescribeScalingPolicies result: ");
	    System.out.println(dspResult);
	} catch (Exception e) {
	    e.printStackTrace();
	    System.err.println("Unable to describe scaling policy: ");
	    System.err.println(e.getMessage());
	}
	
	System.out.println();

	// Remove the scalable target
	DeregisterScalableTargetRequest delSTRequest = new DeregisterScalableTargetRequest()
		.withServiceNamespace(ns)
		.withScalableDimension(tableWCUs)
		.withResourceId(resourceID);

	try {
	    aaClient.deregisterScalableTarget(delSTRequest);
	} catch (Exception e) {
	    System.err.println("Unable to deregister scalable target: ");
	    System.err.println(e.getMessage());
	}

	// Verify that the scalable target was removed
	DescribeScalableTargetsRequest dscRequest = new DescribeScalableTargetsRequest()
		.withServiceNamespace(ns)
		.withScalableDimension(tableWCUs)
		.withResourceIds(resourceID);

	try {
	    DescribeScalableTargetsResult dsaResult = aaClient.describeScalableTargets(dscRequest);
	    System.out.println("DescribeScalableTargets result: ");
	    System.out.println(dsaResult);
	    System.out.println();
	} catch (Exception e) {
	    System.err.println("Unable to describe scalable target: ");
	    System.err.println(e.getMessage());
	}

    }

}
```