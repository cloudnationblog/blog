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

The Parameter Store is a versatile tool that can be used in a variety of situations. Here are a few use cases that demonstrate its capabilities:

**Serverless application running in AWS Lambda:** Storing JSON input data in the Parameter Store allows you to update the data without redeploying the Lambda function. This makes the application more dynamic and easier to manage. Consider you have a serverless application running in AWS Lambda that takes a JSON input to perform an action. The JSON data can be passed by adding a JSON file to the Lambda function's package, but assuming this JSON data needs to be updated frequently, it would require the Lambda function to be re-deployed every time. Systems Manager can be a good tool here. Storing the JSON data in systems manager provides you more flexibility and security. The configuration data lives outside of the application code and the code can be more dynamic. Also in this case, now the same Lambda function can serve multiple application workloads by using a separate parameter store location for each.

**Simple bash script that calls an API:** Storing an API key as a SecureString in the Parameter Store allows you to change the key without modifying the script. This makes it easier to manage the key and reduces the risk of exposing it in the code.

**Unique configuration for different environments:** Storing application configuration data in a hierarchical structure in the Parameter Store allows you to manage different configurations for different environments. This makes it easier to manage the configurations and reduces the risk of errors.

{% include lightbox.html src="aws-parameter-store-hierarchy.png" data="group" %}

## Usage Examples

Let's create `parameter1` in parameter store.

{% include lightbox.html src="aws-parameter-store-parameter1.png" data="group" %}

Here are a few examples of how to use the Parameter Store  and retrieve the above parameter using different programming languages:

### Python3 with boto3

This example shows how to retrieve a parameter value using the `boto3` library in `Python3`. This is useful if you need to integrate the Parameter Store into your Python application.

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

### AWS CLI

This example shows how to retrieve a parameter value using the AWS CLI. This is useful if you need to retrieve a parameter value from the command line or from a shell script.

```shell
parameterPath="parameter1"
echo "Parameter Path: $parameterPath"
parameter_value=$(aws ssm get-parameter --name $parameterPath --query "Parameter.Value" --output text)
echo "The parameter value is: $parameter_value"

# Output
Parameter Path: parameter1
The parameter value is: Hello There!
```

[AWS CLI SSM Documentation](https://awscli.amazonaws.com/v2/documentation/api/latest/reference/ssm/get-parameter.html)


## Additional Notes

**JSON objects:** You can store JSON objects in the Parameter Store, but be aware of the maximum allowed characters limit for the parameter. Removing the JSON whitespace and indentations will free up space.

**SecureString decryption:** If you store a parameter value as a `SecureString`, be sure to decrypt it upon retrieval. This can be done using the `WithDecryption` parameter in boto3 or the `--with-decryption` flag in the AWS CLI.

- Python3:

```python
parameter = ssm.get_parameter(
    Name=path, WithDecryption=True
)
```
- AWS CLI:

```shell
aws ssm get-parameter --name $parameterPath --query "Parameter.Value" --with-decryption --output text
```

If you are looking for a tool to manage secrets and only secrets, AWS Secrets Manager may be a better service for the job. It provides a centralized store for secrets like API keys, database passwords, and SSH keys, and allows you to rotate them automatically.
{: .notice--info}

## Summary

AWS Systems Manager Parameter Store is a valuable tool for managing configuration data for your applications. 

With features like hierarchical parameter structures and secure string storage, Parameter Store provides flexibility and security for your application's configuration data. Using tools like `boto3` and the `AWS CLI`, you can easily integrate Parameter Store into your applications and streamline your configuration management processes.
