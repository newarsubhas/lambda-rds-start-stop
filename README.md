# AWS RDS START / STOP

RDS Start / Stop is a Lambda Function code aims to stop/start RDS's on a scheduled basis.

And then before creating a lambda function, we need to create a IAM Role. Below you can find CFN template or IAM Policy Raw for this role. 

Policy
```
{
    "Version": "2012-10-17",
    "Statement": [        
        {
            "Action": [
                "rds:StopDBInstance",
                "rds:StartDBInstance"
            ],
            "Effect": "Allow",
            "Resource": "*"
        }
    ]
}
```

or CFN

```
  LambdaRdsStartStopRole:
    Type: 'AWS::IAM::Role'
    Condition: IsNotInfra
    Properties:
      AssumeRolePolicyDocument:
        Version: '2012-10-17'
        Statement:
          - Effect: Allow
            Principal:
              Service:
                - rds.amazonaws.com
                - lambda.amazonaws.com
            Action: 'sts:AssumeRole'
      Path: /
      Policies:
        - PolicyName: "lambda-rds-start-stop"
          PolicyDocument:
            Version: '2012-10-17'
            Statement:
              - 
                Action:
                  - 'rds:StopDBInstance'
                  - 'rds:StartDBInstance'
                Resource: '*'
                Effect: Allow
      RoleName: "LambdaRdsStartStop"
```

And now we can create a lambda function. 

 1. Go to AWS console and proceeed with a nodeJS 6.10 Lambda function and Lambda IAM Role
 2. Upload the zip code
 3. And create the lambda function.

## Running the tests

To test your function, on your lambda function page click actions > configure test event and put below json.

```
{ "instances": ["rds-instance-id"], "action":"stop" }
```

and after it's stopped, test with another config event;

```
{ "instances": ["rds-instance-id"], "action":"start" }
```

Note: Before using this on your Production or Real stack, take snapshot or test it on your dev environment.

### Scheduling

For scheduling the lambda function;

1. Go to Cloudwatch > rules page and click "create rule" button.
2. On the left side of creation page, fill your event source. Example: Schedule | Cron | 0 5 ? * MON-FRI * => That will run every day from Monday to Friday at 5:00 AM.
3. On the right side of creation page, click "add target" button and find your Lambda Function.
4. Click "configure test input" and choose " Constant (JSON text)".
5. Write with the below JSON's for your needs .

#### Stopping the Instances: 

```
{ "instances": ["rds-instance-id"], "action":"stop" }
```

#### Starting the Instances:

```
{ "instances": ["rds-instance-id"], "action":"start" }
```
