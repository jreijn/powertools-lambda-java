#  Powertools for AWS Lambda (Java) - Batch Example with SAM on GraalVM

This project contains examples of Lambda function using the batch processing module of Powertools for AWS Lambda (Java).
For more information on this module, please refer to the
[documentation](https://docs.powertools.aws.dev/lambda-java/utilities/batch/).

Three different examples and SAM deployments are included, covering each of the batch sources:

* [SQS](src/main/java/org/demo/batch/sqs) - SQS batch processing
* [Kinesis Streams](src/main/java/org/demo/batch/kinesis) - Kinesis Streams batch processing
* [DynamoDB Streams](src/main/java/org/demo/batch/dynamo) - DynamoDB Streams batch processing

## Deploy the sample application

- Set the environment to use GraalVM

```shell
export JAVA_HOME=<path to GraalVM>
```

## Build the sample application

- Build the Docker image that will be used as the environment for SAM build:

```shell
docker build --platform linux/amd64 . -t powertools-examples-batch-sam-graalvm
```

- Build the SAM project using the docker image

```shell
sam build --use-container --build-image powertools-examples-batch-sam-graalvm
```

#### [Optional] Building with -SNAPSHOT versions of PowerTools

- If you are testing the example with a -SNAPSHOT version of PowerTools, the maven build inside the docker image will fail. This is because the -SNAPSHOT version of the PowerTools library that you are working on is still not available in maven central/snapshot repository.
  To get around this, follow these steps:
    - Create the native image using the `docker` command below on your development machine. The native image is created in the `target` directory.
        - `` docker run --platform linux/amd64  -it -v `pwd`:`pwd` -w `pwd` -v ~/.m2:/root/.m2 powertools-examples-batch-sam-graalvm mvn clean -Pnative-image package -DskipTests ``
    - Edit the [`Makefile`](Makefile) remove this line
        - `mvn clean package -P native-image`
    - Build the SAM project using the docker image
        - `sam build --use-container --build-image powertools-examples-batch-sam-graalvm`

This sample is based on Serverless Application Model (SAM). To deploy it, check out the instructions for getting
started with SAM in [the examples directory](../../README.md)

This sample contains three different deployments, depending on which batch processor you'd like to use, you can
change to the subdirectory containing the example SAM template, and deploy. For instance, for the SQS batch
deployment:

```bash
cd deploy/sqs
sam build
sam deploy --guided
```

## Test the application

Each of the examples uses a Lambda scheduled every 5 minutes to push a batch, and a separate lambda to read it. To
see this in action, we can simply tail the logs of our stack:

```bash
sam logs --tail $STACK_NAME
```