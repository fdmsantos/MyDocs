## Filter Events

```bash
{ ( ($.eventSource = "iam.amazonaws.com") && ($.requestParameters.userName = "sceptre") ) }
{ ($.eventSource = "iam.amazonaws.com") }
{ ( ($.eventSource = "iam.amazonaws.com") && (($.eventName = "Add*") || ($.eventName = "Attach*") || ($.eventName = "Change*") || ($.eventName = "Create*") || ($.eventName = "Deactivate*") || ($.eventName = "Delete*") || ($.eventName = "Detach*") || ($.eventName = "Enable*") || ($.eventName = "Put*") || ($.eventName = "Remove*") || ($.eventName = "Set*") || ($.eventName = "Update*") || ($.eventName = "Upload*")) ) }
{ ( ($.eventSource = "iam.amazonaws.com") && (($.eventName = "Put*Policy") || ($.eventName = "Attach*") || ($.eventName = "Detach*") || ($.eventName = "Create*") || ($.eventName = "Update*") || ($.eventName = "Upload*") || ($.eventName = "Delete*") || ($.eventName = "Remove*") || ($.eventName = "Set*")) ) }
```