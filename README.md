# lambda-runtime-bash

## Install packages

```
make install
```

## Lambda Custom Runtime

Updated: 2019/04/11

- uname -a
  ```
  Linux 169.254.52.125 4.14.88-90.76.amzn2.x86_64 #1 SMP Thu Jan 10 00:27:36 UTC 2019 x86_64 x86_64 x86_64 GNU/Linux
  ```

## Preparing for installation

### AWS IAM Execute role

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "lambda:*"
            ],
            "Resource": "*"
        },
        {
            "Effect": "Allow",
            "Action": [
                "s3:ListBucket"
            ],
            "Resource": [
                "arn:aws:s3:::<$S3_BUCKET>"
            ]
        },
        {
            "Effect": "Allow",
            "Action": "s3:GetObject",
            "Resource": [
                "arn:aws:s3:::<$S3_BUCKET>/*"
            ]
        }
    ]
}
```

### AWS IAM Lambda role

Create a Lambda service role.
Service policy is below.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "logs:CreateLogStream",
                "logs:PutLogEvents"
            ],
            "Resource": "arn:aws:logs:<$AWS_REGION>:<$AWS_ACCOUNT_ID>:log-group:/aws/lambda/*"
        },
        {
            "Effect": "Allow",
            "Action": "logs:CreateLogGroup",
            "Resource": "arn:aws:logs:<$AWS_REGION>:<$AWS_ACCOUNT_ID>:*"
        },
        {
            "Effect": "Allow",
            "Action": [
                "s3:ListBucket"
            ],
            "Resource": [
                "arn:aws:s3:::<$S3_BUCKET>"
            ]
        },
        {
            "Effect": "Allow",
            "Action": "s3:*Object",
            "Resource": [
                "arn:aws:s3:::<$S3_BUCKET>/*"
            ]
        }
    ]
}
```

## Installation

### Environment variables

| Name | Description |
| --- | --- |
| AWS_DEFAULT_REGION | AWS region |
| AWS_PROFILE | AWS profile name |
| LAMBDA_ROLE | AWS IAM role name |
| S3_BUCKET | Upload S3 bucket |
| STACK_NAME | Stack Name for AWS SAM |


### Build Lambda function with AWS CLI

1. Buuild lambda function for build layer

```
make build
```

2. Waiting for creating s3://$S3_BUCKET/provided.tgz (about 1-2min)

3. Build lambda layer

```
make build_layer
```

4. Build lambda sample function with layer

```
make build_sample_cli
```

5. Finally, clean all resources

```
make clean_clean
```

### Build Lambda function with AWS SAM

1. Edit Lambda Role name [sam-template-with-layer.yaml]

2. Build lambda layer and sample function

```
AWS_PROFILE=<AWS Profile> AWS_DEFAULT_REGION=<AWS Regionn> \
STACK_NAME=<Stack Name> S3_BUCKET=<S3 Bucket Name> \
make build_sam
```

3. Deploy lambda layer and sample function

```
AWS_PROFILE=<AWS Profile> AWS_DEFAULT_REGION=<AWS Regionn> \
STACK_NAME=<Stack Name> S3_BUCKET=<S3 Bucket Name> \
make deploy_sam
```

### Clean all resources

```
make clean_all
```
