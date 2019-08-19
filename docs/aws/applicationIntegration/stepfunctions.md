# Task

## Submit Batch Job

```json
"Submit Batch Job": {
  "Type": "Task",
  "Resource": "arn:<PARTITION>:states:::batch:submitJob.sync",
  "Parameters": {
    "JobName": "BatchJobNotification",
    "JobQueue": "<BATCH_QUEUE_ARN>",
    "JobDefinition": "<BATCH_JOB_DEFINITION_ARN>"
  },
  "Next": "Notify Success",
  "Catch": [
    {
      "ErrorEquals": [ "States.ALL" ],
      "Next": "Notify Failure"
    }
  ]
}
```

## Notify

```json
"Notify Success": {
  "Type": "Task",
  "Resource": "arn:<PARTITION>:states:::sns:publish",
  "Parameters": {
    "Message": "Batch job submitted through Step Functions succeeded",
    "TopicArn": "<SNS_TOPIC_ARN>"
  },
  "End": true
},
```

## Fargate Task

```json
"Run Fargate Task": {
  "Type": "Task",
  "Resource": "arn:<PARTITION>:states:::ecs:runTask.sync",
  "Parameters": {
    "LaunchType": "FARGATE",
    "Cluster": "<ECS_CLUSTER_ARN>",
    "TaskDefinition": "<ECS_TASK_DEFINITION_ARN>",
    "NetworkConfiguration": {
      "AwsvpcConfiguration": {
        "Subnets": [
          "${subnetAz1}",
          "${subnetAz2}"
        ],
        "AssignPublicIp": "ENABLED"
      }
    }
  },
  "Next": "Notify Success",
  "Catch": [
    {
      "ErrorEquals": [ "States.ALL" ],
      "Next": "Notify Failure"
    }
  ]
},
```

## Read Next Message from Dynamo DB

```json
"Read Next Message from DynamoDB": {
  "Type": "Task",
  "Resource": "arn:<PARTITION>:states:::dynamodb:getItem",
  "Parameters": {
    "TableName": "<DYNAMO_DB_TABLE_NAME>",
    "Key": {
      "MessageId": {"S.$": "$.List[0]"}
    }
  },
  "ResultPath": "$.DynamoDB",
  "Next": "Send Message to SQS"
},
```

## Send Message to SQS

```json
"Send Message to SQS": {
  "Type": "Task",
  "Resource": "arn:<PARTITION>:states:::sqs:sendMessage",
  "Parameters": {
    "MessageBody.$": "$.DynamoDB.Item.Message.S",
    "QueueUrl": "<SQS_QUEUE_URL>"
  },
  "ResultPath": "$.SQS",
  "Next": "Pop Element from List"
},
```

## Retry

```json
"Submit Job": {
  "Type": "Task",
  "Resource":
  "arn:<PARTITION>:lambda:::function:SubmitJob",
  "ResultPath": "$.guid",
  "Next": "Wait X Seconds",
  "Retry": [
    {
      "ErrorEquals": ["States.ALL"],
      "IntervalSeconds": 1,
      "MaxAttempts": 3,
      "BackoffRate": 2
    }
  ]
}
```

## Start an execution of another state machine and continue

```json
"Start new workflow and continue": {
  "Comment": "Start an execution of another state machine and continue",
  "Type": "Task",
  "Resource": "arn:<PARTITION>:states:::states:startExecution",
  "Parameters": {
    "StateMachineArn": "<STATE_MACHINE_ARN>",
    "Input": {
      "NeedCallback": false,
      "AWS_STEP_FUNCTIONS_STARTED_BY_EXECUTION_ID.$": "$$.Execution.Id"
    }
  },
  "Next": "Start in parallel"
}
```

## Callback Pattern

```json
"Start Task And Wait For Callback": {
  "Type": "Task",
  "Resource": "arn:<PARTITION>:states:::sqs:sendMessage.waitForTaskToken",
  "Parameters": {
    "QueueUrl": "<SQS_QUEUE_URL>",
    "MessageBody": {
      "MessageTitle": "Task started by Step Functions. Waiting for callback with task token.",
      "TaskToken.$": "$$.Task.Token"
    }
  },
  "Next": "Notify Success",
  "Catch": [
    {
      "ErrorEquals": [ "States.ALL" ],
      "Next": "Notify Failure"
    }
  ]
}
```

## Catch

```json
"HelloWorld": {
  "Type": "Task",
  "Resource": "arn:aws:lambda:REGION:ACCOUNT_ID:function:FUNCTION_NAME",
  "Catch": [
    {
      "ErrorEquals": ["CustomError"],
      "Next": "CustomErrorFallback"
    },
    {
      "ErrorEquals": ["States.TaskFailed"],
      "Next": "ReservedTypeFallback"
    },
    {
      "ErrorEquals": ["States.ALL"],
      "Next": "CatchAllFallback"
    }
  ],
  "End": true
}
```

# Parallel

```json
"Start in parallel": {
  "Comment": "Start two executions of the same state machine in parallel",
  "Type": "Parallel",
  "End": true,
  "Branches": [
    {
      "StartAt": "Start new workflow and wait for completion",
      "States": {
        "Start new workflow and wait for completion": {
          "Comment": "Start an execution and wait for it to complete",
          "Type": "Task",
          "Resource": "arn:<PARTITION>:states:::states:startExecution.sync",
          "Parameters": {
            "StateMachineArn": "<STATE_MACHINE_ARN>",
            "Input": {
              "NeedCallback": false,
              "AWS_STEP_FUNCTIONS_STARTED_BY_EXECUTION_ID.$": "$$.Execution.Id"
            }
          },
          "OutputPath": "$.Output",
          "End": true
        }
      }
    },
    {
      "StartAt": "Start new workflow and wait for callback",
      "States": {
        "Start new workflow and wait for callback": {
          "Comment": "Start an execution and wait for it to call back with a task token",
          "Type": "Task",
          "Resource": "arn:<PARTITION>:states:::states:startExecution.waitForTaskToken",
          "Parameters": {
            "StateMachineArn": "<STATE_MACHINE_ARN>",
            "Input": {
              "NeedCallback": true,
              "AWS_STEP_FUNCTIONS_STARTED_BY_EXECUTION_ID.$": "$$.Execution.Id",
              "TaskToken.$": "$$.Task.Token"
            }
          },
          "End": true
        }
      }
    }
  ]
}
```

# Choice

## If

```json
"Job Complete?": {
  "Type": "Choice",
  "Choices": [
    {
      "Variable": "$.status",
      "StringEquals": "FAILED",
      "Next": "Job Failed"
    },
    {
      "Variable": "$.status",
      "StringEquals": "SUCCEEDED",
      "Next": "Get Final Job Status"
    }
  ],
  "Default": "Wait X Seconds"
}
```

## For Loop

```json
"For Loop Condition": {
  "Type": "Choice",
  "Choices": [
    {
      "Not": {
        "Variable": "$.List[0]",
        "StringEquals": "DONE"
      },
      "Next": "Read Next Message from DynamoDB"
    }
  ],
  "Default": "Succeed"
}
```

# Pass

## Hello World

```json
{
  "Comment": "A Hello World example of the Amazon States Language using a Pass state",
  "StartAt": "HelloWorld",
  "States": {
    "HelloWorld": {
      "Type": "Pass",
      "Result": "Hello World!",
      "End": true
    }
  }
}

```

## Pop Element from List

```json
"Pop Element from List": {
  "Type": "Pass",
  "Parameters": {
    "List.$": "$.List[1:]"
  },
  "Next": "For Loop Condition"
}
```

# Succeed

```json
"Succeed": {
  "Type": "Succeed"
}
```

# Fail

```json
"Job Failed": {
  "Type": "Fail",
  "Cause": "AWS Batch Job Failed",
  "Error": "DescribeJob returned FAILED"
}
```

# Wait

```json
"Wait X Seconds": {
  "Type": "Wait",
  "SecondsPath": "$.wait_time",
  "Next": "Get Job Status"
}
```