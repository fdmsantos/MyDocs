# SAM

```bash
sam local invoke "myapp" -e event.json
sam package --template-file template.yaml --s3-bucket mybucket --output-template-file packaged.yaml
sam deploy --template-file packaged.yaml --stack-name mySafeDeployStack --capabilities CAPABILITY_IAM
```

# Runtimes

## Go

[Link](https://github.com/asabi/hello_go_lambda)

```go
import (
    "github.com/aws/aws-sdk-go/aws"
    "github.com/aws/aws-sdk-go/aws/session"
    "github.com/aws/aws-sdk-go/service/lambda"

    "fmt"
    "os"
)


sess := session.Must(session.NewSessionWithOptions(session.Options{
    SharedConfigState: session.SharedConfigEnable,
}))

svc := lambda.New(sess, &aws.Config{Region: aws.String("eu-west-1")})

result, err := svc.ListFunctions(nil)
if err != nil {
    fmt.Println("Cannot list functions")
    return false, nil
}

fmt.Println(result)
return true, nil
```

# Deploy Lambda With Dependencies

## Python

```bash
mkdir project
python3 -m venv venv
source venv/bin/activate
(venv) pip install requests
(venv) touch my_handler.py
(venv) pip freeze > requirements.txt
(venv) pip install -r requirements.txt -t .
(venv) deactivate
rm -rf venv
# zip all folder (Not project folder, but the files inside the project folder) and upload to AWS Lambda
```