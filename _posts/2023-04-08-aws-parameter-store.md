---
title:  "Using AWS Parameter Store as a Configuration and Variable Store"
last_modified_at: 2023-04-08
categories: ["AWS"]
description: Learn how AWS Systems Manager Parameter Store can help you securely store and manage configuration data for your applications. Discover the benefits of using Parameter Store, including hierarchical parameter structures and secure string storage, and see how you can easily integrate it into your applications using tools like boto3 and the AWS CLI. With Parameter Store, you can improve the flexibility, security, and scalability of your applications and simplify your configuration management processes.
header:
   teaser: /images/blog/aws-parameter-store.png
   og_image: /images/blog/aws-parameter-store.png
---

AWS Systems Manager Parameter Store is a powerful tool that allows you to store and manage configuration data for your applications. With Parameter Store, you can securely store secrets, configuration values, and other data that your applications need, and access them from your code at runtime. This provides more flexibility, security, and scalability for your applications, and makes it easier to manage configuration data across multiple services and environments.


## Use Cases

- Consider you have a serverless application running in AWS Lambda that takes a JSON input to perform an action. The JSON data can be passed by adding a JSON file to the Lambda function's package, but assuming this JSON data needs to be updated frequently, it would require the Lambda function to be re-deployed every time. Systems Manager can be a good tool here. Storing the JSON data in systems manager provides you more flexibility and security. The configuration data lives outside of the application code and the code can be more dynamic. Also in this case, now the same Lambda function can serve multiple application workloads by using a separate parameter store location for each.
- A simple bash script that calls some sort of API and authenticates using an API key would need the API key to be supplied somehow. Hardcoding the key directly into the script may not be a good idea. Parameter Store allows storing the key as a `SecureString` that can be retrieved in the script. Again allowing the code to be independent of the API key. Changing the API key is just a matter of updating the parameter, no need to touch the script!
- Another great feature that parameter store provides is the hierarchical parameter structure. You can create parameters as if they were nested. For instance, consider an application `myapplication` that uses a unique configuration for the Production and Development environments, the parameter structure now may look like:
    - `/myapplication`
    - `/myapplication/development`
    - `/myapplication/production`

    {% include lightbox.html src="aws-parameter-store-hierarchy.png" data="group" %}

## Usage Examples

Let's create `parameter1` in parameter store.

{% include lightbox.html src="aws-parameter-store-parameter1.png" data="group" %}

### Parameter Store with boto3

This example is for a function written in `Python3` that calls parameter store using the `boto3` library.

```python
import boto3
import sys

def get_parameter_value(path):
    # Parametr store path is passed in as a function argument
    ssm = boto3.client('ssm')
    parameter = ssm.get_parameter(Name=path)
    print("Parameter Path: " + path)
    print("The parameter value is: " + parameter['Parameter']['Value'])
    return None

get_parameter_value(sys.argv[1])
```

Run:

```shell
python3 parameter.py parameter1

# Output
Parameter Path: parameter1
The parameter value is: Hello There!
```

[boto3 SSM Documentation](https://boto3.amazonaws.com/v1/documentation/api/latest/reference/services/ssm.html)

### Parameter Store with AWS CLI

The same result as the above python function can be achieved by running the following shell commands:

```shell
$parameterPath="parameter1"
echo "Parameter Path: $parameterPath"
parameter_value=$(aws ssm get-parameter --name $parameterPath --query "Parameter.Value" --output text)
echo "The parameter value is: $parameter_value"

# Output
Parameter Path: parameter1
The parameter value is: Hello There!
```

[AWS CLI SSM Documentation](https://awscli.amazonaws.com/v2/documentation/api/latest/reference/ssm/get-parameter.html)


## Additional Notes

- You can also add a `JSON` object to parameter store. However, be considerate of the the maximum allowed characters limit for the parameter. Removing the JSON whitespace and indentions will make some room as well.
- Values of type `SecureString` should be decrypted upon retrieval.
    - In python:
        ```python
        parameter = ssm.get_parameter(
            Name=path, WithDecryption=True
        )
        ```
    - AWS CLI:
        ```shell
        aws ssm get-parameter --name $parameterPath --query "Parameter.Value" --with-decryption --output text
        ```
- If you are strictly looking for a tool to manage secrets and only secrets, *AWS Secrets Manager* may be a better service for the job.

## Summary

AWS Systems Manager Parameter Store is a valuable tool for managing configuration data for your applications. 

With features like hierarchical parameter structures and secure string storage, Parameter Store provides flexibility and security for your application's configuration data. Using tools like `boto3` and the `AWS CLI`, you can easily integrate Parameter Store into your applications and streamline your configuration management processes.
