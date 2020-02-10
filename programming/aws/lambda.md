Lambda
===============

## Getting Started Guide Notes
- [src](https://docs.aws.amazon.com/lambda/latest/dg/getting-started.html)
- The handler is what gets run each time an event comes in
  - It receives the event and a context object
- If your handler completes successfully, the runtime will cache all the code/variables defined outside of your handler so that new events only have to re-compile/execute your handler
- You have access to local storage in `/tmp` directory
- Layers are so that you only have to redeploy your function- because otherwise you have to send up all the dependencies.  This is most useful in a dev/test scenario.
- to process items from a stream or queue, use *event source mapping*
- you can have async lambdas that only fire off some event to a queue, but the return a response before any return values or confirmations are provided
- AWS SAM is an extension for the AWS CloudFormation template language that lets you define serverless applications at a higher level.
- You can also use the AWS SAM CLI

## Event Source Mapping
- [src](https://docs.aws.amazon.com/lambda/latest/dg/invocation-eventsourcemapping.html)


- AWS Serverless Application vs Lambda Project (e.g. C# class with a function)
  - Lambda Project
    - Triggers
      - Events
      - Another application
  - Serverless Application
    - Uses API Gateway Proxy Integration
    - Forwards all requests to the lambda function
    - Then it gets passed to AWS .NET Core Pipeline



- [try this lab](https://aws.amazon.com/getting-started/projects/build-serverless-web-app-lambda-apigateway-s3-dynamodb-cognito/)

- [lambda applications](https://docs.aws.amazon.com/lambda/latest/dg/deploying-lambda-apps.html)

- using other frameworks - check the blueprints
- big picture: we're not running locally so much anymore (hence sandbox environments)

- continue here https://aws.amazon.com/serverless/

## Integration with Other AWS Services
- Example Triggers
  - AWS Resource Lifecycle events
  - Respond to HTTP requests
  - Consume events from a queue
  - Run on a schedule
- Each of these services will send an event (each with a different structure) formatted as JSON and the runtime will deserialize to an object which gets passed to your function handler.
- For compiled languages (C#, Java, Go), Lambda libraries provide definitions for event types
- **Pull**: [Event Source mapping](https://docs.aws.amazon.com/lambda/latest/dg/invocation-eventsourcemapping.html) is closer to a 'pull' trigger where Lambda will read data from another service and generate an event and invoke the function.
  - SQS
  - DynamoDB
  - Kinesis
  - Requires Lambda to have the `execution role` on the other service
- **Push**: Other triggers are more 'push' oriented
  - To set this up:
    - Grant the other service permission in the function's resource-based policy
    - Configure service to generate events and invoke your function
  - Invocations can be synchronous or asynchronous
    - Synchronous:
      - Might retry on errors
      - Elastic Load Balancing
      - Cognito
      - Lex
      - Alexa
      - API Gateway
      - CloudFront
      - Kinesis
      - Step Functions
    - Aynchronous:
      - Lambda queues (internally) the event before passing it to function
      - Other services gets a success on queue
      - If Error, Lambda retires and can send failed events to a dead-letter queue that you have to configure 
      - S3
      - SNS
      - Simple Email Service
      - CloudFormation
      - CloudWatch
      - CodeCommit
      - Config
      - Iot
      - CodePipeline

## Integration with API Gateway
- Learn about stage variables: https://docs.aws.amazon.com/apigateway/latest/developerguide/stage-variables.html
- Referencing different version of a lambda: https://aws.amazon.com/blogs/compute/using-api-gateway-stage-variables-to-manage-lambda-functions/
- What is th point of lambda aliases?
- what about lambda timeouts? cold start, etc?
- when to use lambda callback (for return values) in node platform?


