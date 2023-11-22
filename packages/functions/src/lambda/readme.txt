
docker build --platform linux/amd64 -t docker-image:test .

aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin 806877325588.dkr.ecr.us-east-1.amazonaws.com

aws ecr create-repository --repository-name hello-world --region us-east-1 --image-scanning-configuration scanOnPush=true --image-tag-mutability MUTABLE

{
    "repository": {
        "repositoryArn": "arn:aws:ecr:us-east-1:806877325588:repository/hello-world",
        "registryId": "806877325588",
        "repositoryName": "hello-world",
        "repositoryUri": "806877325588.dkr.ecr.us-east-1.amazonaws.com/hello-world",
        "createdAt": "2023-11-21T19:14:56-07:00",
        "imageTagMutability": "MUTABLE",
        "imageScanningConfiguration": {
            "scanOnPush": true
        },
        "encryptionConfiguration": {
            "encryptionType": "AES256"
        }
    }
}

docker tag docker-image:test 806877325588.dkr.ecr.us-east-1.amazonaws.com/hello-world:latest

docker push 806877325588.dkr.ecr.us-east-1.amazonaws.com/hello-world:latest

*** had to build lambda-ex role by hand before the following worked, ** also had to change default region to us-east-1 in .aws

aws lambda create-function \
  --function-name hello-world \
  --package-type Image \
  --code ImageUri=806877325588.dkr.ecr.us-east-1.amazonaws.com/hello-world:latest \
  --role arn:aws:iam::806877325588:role/lambda-ex

aws lambda invoke --function-name hello-world response.json


