![image](https://github.com/user-attachments/assets/53ab3c26-0ced-47bc-9b21-6aeb652a968a)# Level 6
url: http://level6-cc4c404a8a8b876167f5e70a7d8c9880.flaws.cloud/ddcc78ff/

<kbd>![image](https://github.com/user-attachments/assets/eb4c364a-c80b-4bb4-8a86-bffb0f17e0cb)</kbd>

This time we are going to find policies and access attached to the given keys, also it said the there is SecurityAudit policy attached to these keys.

```
Access key ID: AKIAJFQ6E7BY57Q3OBGA
Secret: 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
```

Lets proceed and add this new access to our profile using `aws configure` command

<kbd>![image](https://github.com/user-attachments/assets/24a8ba8e-0178-48f1-a2a6-38d871464394)</kbd>

And if we want to check our listed profiles we can also use 

```
aws configure list-profiles
```
<kbd>![image](https://github.com/user-attachments/assets/8d7fc921-b57c-4db9-97ac-1a3e9b30cfe1)</kbd>

And then we will enumerate using the `get-user` command under the `iam` module of aws

```
aws iam get-user --profile level6
```
<kbd>![image](https://github.com/user-attachments/assets/ae2c5ebc-488c-4742-a861-e533eda16b2c)</kbd>

we'll take note of the username
```
 "UserName": "Level6"
```

And we'll find policies attached to it using `list-attached-user-policies`

```
└─$ aws iam list-attached-user-policies --user-name level6 --profile level6 
{
    "AttachedPolicies": [
        {
            "PolicyName": "MySecurityAudit",
            "PolicyArn": "arn:aws:iam::975426262029:policy/MySecurityAudit"
        },
        {
            "PolicyName": "list_apigateways",
            "PolicyArn": "arn:aws:iam::975426262029:policy/list_apigateways"
        }
    ]
}
  
```
<kbd>![image](https://github.com/user-attachments/assets/3e67ace5-2786-4bc4-8ec1-ad9d08111210)</kbd>


we'll take the `PolicyArn` of the list_apigateways, to find the versionid, and as seen `v4` is our version

<kbd>![image](https://github.com/user-attachments/assets/8df2443b-6c80-47ce-b18a-679ea20f356d)</kbd>


Now that you have the ARN and the version id, you can see what the actual policy is: 

```
 aws iam get-policy-version  --policy-arn arn:aws:iam::975426262029:policy/list_apigateways --version-id v4 --profile level6 
```

<kbd>![image](https://github.com/user-attachments/assets/a3c18607-9b7a-4c15-8f0e-e79779f7a75e)</kbd>


This tells us using this policy we can call "apigateway:GET" on "arn:aws:apigateway:us-west-2::/restapis/*" 

Since the `SecurityAudit policy` lets us see some things about lambdas: 

To check this we can issue 
```

└─$ aws lambda list-functions --region us-west-2 --profile level6
{
    "Functions": [
        {
            "FunctionName": "Level6",
            "FunctionArn": "arn:aws:lambda:us-west-2:975426262029:function:Level6",
            "Runtime": "python2.7",
            "Role": "arn:aws:iam::975426262029:role/service-role/Level6",
            "Handler": "lambda_function.lambda_handler",
            "CodeSize": 282,
            "Description": "A starter AWS Lambda function.",
            "Timeout": 3,
            "MemorySize": 128,
            "LastModified": "2017-02-27T00:24:36.054+0000",
            "CodeSha256": "2iEjBytFbH91PXEMO5R/B9DqOgZ7OG/lqoBNZh5JyFw=",
            "Version": "$LATEST",
            "TracingConfig": {
                "Mode": "PassThrough"
            },
            "RevisionId": "d45cc6d9-f172-4634-8d19-39a20951d979",
            "PackageType": "Zip",
            "Architectures": [
                "x86_64"
            ],
            "EphemeralStorage": {
                "Size": 512
            },
            "SnapStart": {
:

```

So we have a function name called `Level6` 

<kbd>![image](https://github.com/user-attachments/assets/a67289e7-d302-49af-a446-994881066d4a)</kbd>

To get more infromation about the `Level6` function we can use the  lambda's `get-policy` function
```
┌──(aaron㉿kali)-[~/flaws/level6]
└─$ aws lambda get-policy --function-name Level6 --region us-west-2 --profile level6
{
    "Policy": "{\"Version\":\"2012-10-17\",\"Id\":\"default\",\"Statement\":[{\"Sid\":\"904610a93f593b76ad66ed6ed82c0a8b\",\"Effect\":\"Allow\",\"Principal\":{\"Service\":\"apigateway.amazonaws.com\"},\"Action\":\"lambda:InvokeFunction\",\"Resource\":\"arn:aws:lambda:us-west-2:975426262029:function:Level6\",\"Condition\":{\"ArnLike\":{\"AWS:SourceArn\":\"arn:aws:execute-api:us-west-2:975426262029:s33ppypa75/*/GET/level6\"}}}]}",
    "RevisionId": "edaca849-06fb-4495-a09c-3bc6115d3b87"
}
         
```
<kbd>![image](https://github.com/user-attachments/assets/dbd4eaa1-7c16-468c-a0e0-5491829f783b)</kbd>

This tells us we have the ability to execute `arn:aws:execute-api:us-west-2:975426262029:s33ppypa75/*/GET/level6\` 

That "s33ppypa75" is a rest-api-id, which we can then use to find the resource name, we are oing to use the `aws apigateway get-stages` command

```
└─$ aws apigateway get-stages --rest-api-id s33ppypa75 --region us-west-2 --profile level6
{
    "item": [
        {
            "deploymentId": "8gppiv",
            "stageName": "Prod",
            "cacheClusterEnabled": false,
            "cacheClusterStatus": "NOT_AVAILABLE",
            "methodSettings": {},
            "tracingEnabled": false,
            "createdDate": "2017-02-26T19:26:08-05:00",
            "lastUpdatedDate": "2017-02-26T19:26:08-05:00"
        }
    ]
}


```
We can see from the results the  `"stageName": "Prod",` 

**Lambda** functions in aws are called using that `rest-api-id`, `stage name`, `region`, and `resource`. 

 Since we already have these 4 requirements, we combine them all together we will have ` https://s33ppypa75.execute-api.us-west-2.amazonaws.com/Prod/level6`

>- `rest-api-id`  = s33ppypa75 (We get from  `aws lambda get-policy` command )
>- `stage name` = execute-api (We get from  `aws lambda get-policy` command )
>- `region` = us-west-2 
>- resource = Prod/level6  (from `aws apigateway get-stages` )
 

Then we we go to the url, this is what we see

<kbd>![image](https://github.com/user-attachments/assets/b855e61e-de68-4121-9f6b-170b3d3b5238)</kbd>


It tells us to go to `http://theend-797237e8ada164bf9f12cebf93b282cf.flaws.cloud/d730aa2b/`

![image](https://github.com/user-attachments/assets/cefa99cc-f91e-4e78-94bb-0a3e37425492)


The end. 



