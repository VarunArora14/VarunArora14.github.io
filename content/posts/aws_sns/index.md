---
title: "AWS SNS using boto3 SDK"
date: 2024-08-15T22:26:12+05:30
draft: false
tags: ["AWS", "SNS", "Lambda", "boto3"]
---

## Topic in SNS

In Amazon Simple Notification Service (SNS), a **topic is an access point for allowing subscribers to receive notifications or messages.**

**When you publish a message to a topic, SNS sends the message to all the subscribers of that topic**.

## Sending files as attachments

Yes, you can use Amazon Simple Notification Service (SNS) to send a CSV file stored in an Amazon S3 bucket as an attachment in an email notification. Here are the high-level steps to achieve this:

1. Upload the CSV file to an S3 bucket.
2. Create an SNS topic and subscribe an email endpoint to the topic.
3. Create an AWS Lambda function that will be triggered when a new file is uploaded to the S3 bucket. This function should read the CSV file from the S3 bucket and use the SNS publish() API to send an email message to the SNS topic with the CSV file as an attachment.
4. Configure an S3 bucket event to trigger the Lambda function whenever a new CSV file is uploaded to the S3 bucket.

```py
import boto3
from botocore.exceptions import ClientError

def lambda_handler(event, context):
    # Get the S3 bucket and object key from the S3 event notification
    bucket_name = event['Records'][0]['s3']['bucket']['name']
    object_key = event['Records'][0]['s3']['object']['key']

    # Retrieve the CSV file from S3
    s3 = boto3.resource('s3')
    obj = s3.Object(bucket_name, object_key)
    file_content = obj.get()['Body'].read().decode('utf-8')

    # Create an SNS client
    sns = boto3.client('sns')

    # Set the email message subject and body
    subject = 'CSV file notification'
    body = 'Please find the attached CSV file.'

    # Set the attachment filename and content
    attachment_name = object_key
    attachment_content = file_content

    # Publish the SNS message with the CSV file as an attachment
    try:
        response = sns.publish(
            TopicArn='arn:aws:sns:us-east-1:123456789012:my-topic',
            Message=body,
            Subject=subject,
            MessageStructure='string',
            MessageAttributes={
                'attachment': {
                    'DataType': 'Binary',
                    'BinaryValue': attachment_content.encode('utf-8')
                },
                'attachment-name': {
                    'DataType': 'String',
                    'StringValue': attachment_name
                }
            }
        )
        print('SNS message published successfully: {}'.format(response['MessageId']))
    except ClientError as e:
        print('Error publishing SNS message: {}'.format(e))
```

In this example, the Lambda function is triggered by an S3 event notification when a new CSV file is uploaded to the S3 bucket. The function retrieves the file contents from S3 and sends an email message to the SNS topic using the publish() API. The message includes the CSV file as an attachment, which is specified using the MessageAttributes parameter.

Note that you will need to grant the Lambda function permission to access the S3 bucket and the SNS topic. You can do this using AWS Identity and Access Management (IAM) policies
