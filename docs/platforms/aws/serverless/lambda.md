## Build

*Runtime Python 3.6*

[How to create an AWS Lambda python 3.6 deployment package using Docker](https://medium.com/i-like-big-data-and-i-cannot-lie/how-to-create-an-aws-lambda-python-3-6-deployment-package-using-docker-d0e847207dd6)

```bash
sudo docker run -it dacut/amazon-linux-python-3.6
mkdir <DOCKER_PROJEC_DIR>
cd <DOCKER_PROJECT_DIR>
pip3 install <PACKAGE_NAME> -t ./
zip -r <PROJECT_NAME>.zip *
exit
sudo docker ps -a | grep "dacut" | awk '{print $1}'
sudo docker cp <CONTAINER_ID>:<DOCKER_PROJECT_DIR>/<PROJECT_NAME>.zip <LOCAL_PROJECT_PATH>
unzip <LOCAL_PROJECT_PATH>/<PROJECT_NAME>.zip
rm <LOCAL_PROJECT_PATH>/<PROJECT_NAME>.zip

# Add Lambda File to zip
zip -ur <PROJECT_NAME>.zip <PATH_TO_LAMBDA_FUNCTION_FILE>
```