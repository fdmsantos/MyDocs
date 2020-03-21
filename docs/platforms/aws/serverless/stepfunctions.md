## Lambda Task

```json
"GetLinks": {
  "Type": "Task",
  "Resource": "arn:aws:states:::lambda:invoke",
  "Parameters": {
    "FunctionName": "arn:aws:lambda:eu-central-1:951313074793:function:test-api-branch_io-links:$LATEST",
    "Payload": {
      "Input.$": "$"
    }
  },
  "OutputPath": "$.Payload",
  "Next": "SendLinkToSQSQueue"
}
```

## Map

**SQS: Callback Pattern**

```json
"SendLinkToSQSQueue": {
  "Type": "Map",
  "MaxConcurrency": 20,
  "ItemsPath": "$",
  "Iterator": {
    "StartAt": "Send Link to SQS",
    "States": {
      "Send Link to SQS": {
        "Type": "Task",
        "Resource": "arn:aws:states:::sqs:sendMessage.waitForTaskToken",
        "Parameters": {
          "QueueUrl": "https://sqs.eu-central-1.amazonaws.com/951313074793/test--branch_io-queue",
          "MessageBody": {
            "Url.$": "$.MessageBody",
            "TaskToken.$": "$$.Task.Token"
          },
          "MessageAttributes.$": "$.MessageAttributes"
        },
        "End": true
      }
    }
  },
  "Next": "World"
}
```