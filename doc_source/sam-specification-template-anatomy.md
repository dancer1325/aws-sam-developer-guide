# AWS SAM template anatomy<a name="sam-specification-template-anatomy"></a>

* AWS SAM templates
  * == extension of AWS CloudFormation templates 
    * extension == SOME additional components
    * see [AWS CloudFormation Template Reference](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/template-reference.html)
    * -> 👀' format == AWS CloudFormation templates' format 👀
      * see [Template anatomy](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/template-anatomy.html)
      * differences
        + **Transform declaration\.**
          + ⚠️MANDATORY ⚠️
          + _Example:_ `Transform: AWS::Serverless-2016-10-31`
          + identifies an AWS CloudFormation template file -- as an -- AWS SAM template file
          + see [Transform](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/transform-section-structure.html)
        + **Globals section\.**
          + unique to AWS SAM
          + defines properties / common | ALL your serverless functions & APIs
            + _Example:_ `AWS::Serverless::Function`, `AWS::Serverless::Api`,  `AWS::Serverless::SimpleTable`
          + see [Globals section of the AWS SAM template](sam-specification-template-anatomy-globals.md)
        + **Resources section\.**
          + == AWS CloudFormation resources + AWS SAM resources
          + see [AWS SAM resource and property reference](sam-specification-resources-and-properties.md)\.
        + **Parameters section\.**
          + -> `sam deploy --guided` has additional prompts
          + see [sam deploy](sam-cli-command-reference-sam-deploy.md)

## YAML<a name="template-anatomy-outline.yaml"></a>

* _Example:_

    ```
    Transform: AWS::Serverless-2016-10-31
    
    Globals:
      set of globals
    
    Description:
      String
    
    Metadata:
      template metadata
    
    Parameters:
      set of parameters
    
    Mappings:
      set of mappings
    
    Conditions:
      set of conditions
    
    Resources:
      set of resources
    
    Outputs:
      set of outputs
    ```

## Template sections<a name="template-anatomy-sections"></a>

* 👀can be provided / ANY order 👀
  * recommended
    * use the logical order / shown below

**[Transform](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/transform-section-structure.html)**
* 👀required 👀  
* `AWS::Serverless-2016-10-31`
  * MANDATORY
* Additional transforms are OPTIONAL 
* see [Transform](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/transform-section-structure.html)

**[Globals \(optional\)](sam-specification-template-anatomy-globals.md)**  
* 👀required 👀
* unique to AWS SAM
  * == NOT exist | AWS CloudFormation templates
* := properties / common | ALL your serverless functions, APIs, and simple tables
  * == ALL `AWS::Serverless::Function`, `AWS::Serverless::Api`, and `AWS::Serverless::SimpleTable` resources -- will inherit -- them

**[Description \(optional\)](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/template-description-structure.html)**  
* TODO:
A text string that describes the template\.  
This section corresponds directly with the `Description` section of AWS CloudFormation templates\.

**[Metadata \(optional\)](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/metadata-section-structure.html)**  
Objects that provide additional information about the template\.  
This section corresponds directly with the `Metadata` section of AWS CloudFormation templates\.

**[Parameters \(optional\)](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/parameters-section-structure.html)**  
Values to pass to your template at runtime \(when you create or update a stack\)\. You can refer to parameters from the `Resources` and `Outputs` sections of the template\.  
Values that are passed in using the `--parameter-overrides` parameter of the `sam deploy` command—and entries in the configuration file—take precendence over entries in the AWS SAM template file\. For more information about the `sam deploy` command, see [sam deploy](sam-cli-command-reference-sam-deploy.md) in the AWS SAM CLI command reference\. For more information about the configuration file, see [AWS SAM CLI configuration file](serverless-sam-cli-config.md)\.

**[Mappings \(optional\)](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/mappings-section-structure.html)**  
A mapping of keys and associated values that you can use to specify conditional parameter values, similar to a lookup table\. You can match a key to a corresponding value by using the [https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/intrinsic-function-reference-findinmap.html](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/intrinsic-function-reference-findinmap.html) intrinsic function in the `Resources` and `Outputs` sections\.  
This section corresponds directly with the `Mappings` section of AWS CloudFormation templates\.

**[Conditions \(optional\)](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/conditions-section-structure.html)**  
Conditions that control whether certain resources are created or whether certain resource properties are assigned a value during stack creation or update\. For example, you could conditionally create a resource that depends on whether the stack is for a production or test environment\.  
This section corresponds directly with the `Conditions` section of AWS CloudFormation templates\.

**[Resources \(required\)](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/resources-section-structure.html)**  
The stack resources and their properties, such as an Amazon Elastic Compute Cloud \(Amazon EC2\) instance or an Amazon Simple Storage Service \(Amazon S3\) bucket\. You can refer to resources in the `Resources` and `Outputs` sections of the template\.  
This section is similar to the `Resources` section of AWS CloudFormation templates\. In AWS SAM templates, this section can contain AWS SAM resources in addition to AWS CloudFormation resources\.

**[Outputs \(optional\)](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/outputs-section-structure.html)**  
* == values / returned | viewing your stack's properties
  * _Example:_ an output for an S3 bucket name -> can be called `aws cloudformation describe-stacks`  
* == `Outputs` | AWS CloudFormation templates

## Next steps<a name="template-anatomy-next-steps"></a>

* see 
  * [Getting started with AWS SAM](serverless-getting-started.md)
  * [Tutorial: Deploying a Hello World application](serverless-getting-started-hello-world.md)