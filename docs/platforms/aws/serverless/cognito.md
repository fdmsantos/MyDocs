## Change User Status FORCE_CHANGE_PASSWORD

[Link - StackOverflow](https://stackoverflow.com/questions/40287012/how-to-change-user-status-force-change-password)

Use AWS CLI to change the users password.

   * Get a session token for the desired user:

```bash
aws cognito-idp admin-initiate-auth 
    --user-pool-id %USER POOL ID% 
    --client-id %APP CLIENT ID% 
    --auth-flow ADMIN_NO_SRP_AUTH 
    --auth-parameters USERNAME=%USERS USERNAME%,PASSWORD=%USERS CURRENT PASSWORD%
```

   * This will respond with the challenge "NEW_PASSWORD_REQUIRED", 
 other challenge parameters and the users session key. 
Then, run the second command to issue the challenge response:

```bash
aws cognito-idp admin-respond-to-auth-challenge 
    --user-pool-id %USER POOL ID% 
    --client-id %CLIENT ID% 
    --challenge-name NEW_PASSWORD_REQUIRED 
    --challenge-responses NEW_PASSWORD=%DESIRED PASSWORD%,USERNAME=%USERS USERNAME% 
    --session %SESSION KEY FROM PREVIOUS COMMAND with ""%
```