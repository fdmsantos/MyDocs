# Api Gateway

## Integration

### Step Functions

#### Request

```json
{
   "input": "$util.escapeJavaScript($input.json('$'))",
   "stateMachineArn": "${RegisterDeviceStateMachine}"
}
```

#### Response

```json
{
  "stateMachineExecution": "$input.json('$.executionArn').split(':')[7].replace('"', "")"
}
```

## Models

```json
{
  "$schema": "http://json-schema.org/draft-04/schema#",
  "title" : "Request Validator",
  "type" : "object",
  "properties": {
      "DeviceID": {
          "type": "string"
      },
      "Name": {
          "type": "string"
      }
  },
  "required": ["DeviceID", "Name"],
  "additionalProperties": false
}
```