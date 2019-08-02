# Troubleshooting

## WaitCondition

**If WaitCondition times out or returns an error. There is an error in your cloudformation::init code**

1. Analyze cfn-init.log and cfn-wire.log for details collect logs via CloudWatch logs
2. –on-failure DO_NOTHING to keep instance from rolling back so you can log in and examine the logs
3. Most common error: URLs for referenced resources (scripts, MSIs, etc.) are returning HTTP 403 or 404 errors