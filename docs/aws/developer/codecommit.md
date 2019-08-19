# Configure Git to Use AWS Cli Credentials

```bash
git config --global user.name "developer" 
git config --global user.email developer@example.com

git config --global credential.helper '!aws codecommit credential-helper $@'
git config --global credential.UseHttpPath true
```