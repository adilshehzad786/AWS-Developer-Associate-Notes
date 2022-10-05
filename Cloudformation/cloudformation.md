# Infrastructure As Code (CloudFormation)

## Cloud formation Building Blocks

### Stacks

A stack represents a collection of resources to deploy and manage by AWS Cloudformation when you submit a template the resources you configure are provisioned and then make up the stack itself.

### Change Sets

A change set is a description of the changes that will occur to a stack, should you submit the template. if the changes are acceptable, the change set itself can execute on the stack and implement the proposed modifications. This is especially important in situations where there is a potential for data loss.

## Template Structure

AWS Cloudformation uses specific template syntax in JSON or YAML.

```json
{
"AWSTemplateFormatVersion": "2010-09-09",
 "Description": "String Description",
 "Metadata": { },
 "Parameters": { },
 "Mappings": { },
 "Conditions": { },
 "Transform": { },
 "Resources": { },
 "Outputs": { }
}
```

### AWSTemplateFormationVersion

AWSTemplateFormatVersion corresponds to the template version to which this template adheres. Do not confuse this with an API version or the version of the developer template draft. Currently AWS Cloudformation only supports the value “2010-09-09” which you must provide as a literal string.

### Description

The Description section allow you to provide a text explanation of the template purpose or other arbitrary information. The maximum length of the description field is 1024 byte.

### MetaData

The Metadata section of a template allow you to provide structured detail about the template.

### Parameters

You can use Parameteres to provide inputs into your template, which allow for more flexibility in how this template behaves when you deploy it. paramaters values can be set when you create the stack or perform updates.

### Mappings

You can use Mapping section of a template to create a rudimentary lookup tables that you can reference in other sections of your template when you create the stack. for example, mapping usuage is to look up Amazon Ec2 instance AMI ID based on the region and architecture type.

### Conditions

You can use conditions in AWS Cloudformation template to determine when to create a resource or when a property of a resource is defined. Conditional statements make use of intrinsic functions to evaluate multiple inputs against one other.

### Transforms

A templates grow in size and complexity , there may be situaution where you use certian components repeatedly across multiple templates, such as common resources or mappings. Transforms allow you to simplify the template authoring process through a prowerful set of macros you use to reduce the amount of time spent in the authoring process.

**AWS::Serverless Transform**

you can use this tranform to convert SAM template to valid AWS Cloudformation template for deployment.

## Instrinsic Functions

### FN:Base64

This will convert an input string into Base64 .

### FN::CIDR

This will convert an IP address block , subnet count and size mask into valid CIDR notation.

### FN::FindInMap

This will query information stored within the mapping table.

### FN::GetAtt

With GetAtt you can query in other part of the same template.

### FN::Join

AWS Cloudformation support string concatenation with Fn::Join intrinsic function.

### Fn::Select

if you pass a list of values in your template, there need to be a way to select an item from the list based on the index it is in the list. Fn::Select allow you to choose an item in a list based on the zero-based index.

### Fn::Split

FN::Split to create a list of strings by seperating a single string by known delimiter.

### Outputs

Outputs are values that can be made available to use outside a single stack.

## Templates components

- Resources: AWS resources declared in the template (can use `!Ref` function to refer to others resources in the same YAML file)
- Parameters: the dynamic inputs (use with `!Ref` function)
- Mappings: the static variables (use with `!FindInMap[ MapName, TopLevelKey, SecondLevelKey ]` function)
- Outputs: export to be used by other stacks (other stacks use `!ImportValue` function to reference). Exported output names must be unique within region
- Conditionals: List of conditions to perform resource creation (`!If`, `!Not`, `!Equal`...)
- Metadata

### Templates helpers:

- References
- Functions

## Rollbacks

- Stack Creation Fails:
  - Default: everything rolls back (gets deleted)
  - Option to disable rollback and troubleshoot what happened
- Stack Update Fails:
  - The stack automatically rolls back to the previous known working state
  - Ability to see in the log what happened and error messages

## Tips:

- Can I create a dynamic amount of resources?
  
  - No, you can't. Everything in CloudFormation templates has to be declared. Can't perform code generation here
- Is every AWS Service supported in CloudFormation's resources?
  
  - Almost. Only a select few niches are not there yet
  - You can work around that using AWS Lambda Custom Resources
- Exported output names must be unique within region
  
- `!GetAtt`

  ![Untitled](https://user-images.githubusercontent.com/53600644/194138304-0dc664e8-bcfc-456c-9722-7ffe3daaa740.png)

 
- `!Join`
  
  - want to create "a:b:c" ⇒ `!Join [ ":", [a, b, c] ]`
- `!Sub`
  
  - with a Mapping
    
    ```yaml
    Name: !Sub
      - www.${Domain}
      - { Domain: !Ref RootDomainName }
    ```
    
  - without a Mapping
    
    ```yaml
    !Sub 'arn:aws:ec2:${AWS::Region}:${AWS::AccountId}:vpc/${vpc}'
    ```
