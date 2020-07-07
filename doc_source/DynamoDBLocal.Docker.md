# Install the DynamoDB Docker Image<a name="DynamoDBLocal.Docker"></a>

The downloadable version of Amazon DynamoDB is available as a Docker image\. For more information, see [dynamodb\-local](https://hub.docker.com/r/amazon/dynamodb-local)\.

 For an example of using DynamoDB local as part of a REST application built on the AWS Serverless Application Model \(AWS SAM\), see [SAM DynamoDB application for managing orders](https://github.com/aws-samples/aws-sam-java-rest)\. This sample application demonstrates how to use DynamoDB local for testing\. 

## Using Docker Compose<a name="DynamoDBLocal.Docker.Compose"></a>

If you want to run a multi\-container application that also uses the DynamoDB local container, use Docker Compose to define and run all the services in your application, including DynamoDB local\.

**To install and run DynamoDB local with Docker Compose:**

1. Download and install [Docker Desktop](https://www.docker.com/products/docker-desktop)\.

1. Copy the following code to a file and save it as docker\-compose\.yml

   ```
   version: '3.7'
   services:
    dynamodb-local:
      image: amazon/dynamodb-local:latest
      container_name: dynamodb-local
      ports:
       - "8000:8000"
   ```

   if you want to have your application and DynamoDB local be in separate containers use the following yaml file:

   ```
   version: '3.7'
   services:
     dynamodb-local:
       image: amazon/dynamodb-local
       container_name: dynamodb-local
       ports:
         - "8000:8000"
     app-node:
       depends_on:
         - dynamodb-local
       image: banst/awscli
       container_name: app-node
       ports:
        - "8080:8080"
       environment:
         AWS_ACCESS_KEY_ID: 'DUMMYIDEXAMPLE'
         AWS_SECRET_ACCESS_KEY: 'DUMMYEXAMPLEKEY'
       command:
         dynamodb describe-limits --endpoint-url http://dynamodb-local:8000 --region us-west-2
   ```

   This docker\-compose\.yml script creates an `app-node` container and a `dynamodb-local` container\. The script runs a command in the `app-node` container that uses the AWS CLI to connect to the `dynamodb-local` container and describes the account and table limits\.

   To use with your own application image, replace the `image` value in the example below with that of your application's:

   ```
   version: '3.7' 
   services:
    app-node:
      image: location-of-your-dynamodb-demo-app:latest
      container_name: app-node
      ports:
       - "8080:8080"
      depends_on:
       - "dynamodb-local"
      links:
       - "dynamodb-local"
      environment:
       - AWS_ACCESS_KEY_ID='DUMMYIDEXAMPLE'
       - AWS_SECRET_ACCESS_KEY='DUMMYEXAMPLEKEY'
       - REGION='eu-west-1'
    dynamodb-local:
      image: amazon/dynamodb-local:latest
      container_name: dynamodb-local
      ports:
       - "8000:8000"
   ```
**Note**  
The yaml scripts require that you specify an AWS access key and an AWS secret key, but they are not required to be valid AWS keys for you to access DynamoDB local\.

1. Run the following command\-line:

   ```
   docker-compose up
   ```