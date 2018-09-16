# AWS Lambda function that sends emails through SES

This Lambda function receives input from AWS SQS, then sends out emails through AWS SES.

![Alt Architecture Overview Diagram](https://raw.githubusercontent.com/james-hu/jabb-email-sender/master/doc/Overview.png)


## Usage

You just need to put details of the email messages into the input SQS queue.
If configured properly, the lambda function should be able to pick up messages from the input SQS queue
and send them out through SES. Invalid/failed messages will end up in the Dead-Letter-Queue.

### Build

Just run: `mvn clean package` or `./mvnw clean package` or `.\mvnw clean package` 

## Configuration

### SQS Queues

Two SQS queues must exist, one as the input queue, the other as the Dead-Letter-Queue

The input queue should have these configured:
* It triggers the Lambda function.
* It has Dead-Letter-Queue configured on it.

### Handler

The handler class is: `net.sf.jabb.email.sender.LambdaEmailSender`

### Environment variables

* SES_REGION - In which AWS region should SES be used for sending out emails. e.g. `us-west-2`
* DLQ_URL - URL to the Dead-Letter-Queue that all invalid/failed emails should go. e.g. `https://sqs.us-west-2.amazonaws.com/123456789/email-dead-letter-queue`

### Memory usage

256MB is the minimal memory required.

### DLQ

Don't configure DLQ on the Lambda function. Configure it on the input SQS queue instead.

### IAM

The Lambda function needs to run in a role with these policies:

* `AWSLambdaSQSQueueExecutionRole`
* `AWSLambdaBasicExecutionRole`
* These permissions on SES:
  * `SendEmail`
* These permissions on the Dead-Letter-Queue specified by environment variable `DLQ_URL`:
  * `SendMessage`
